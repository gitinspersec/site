---
title: Deploy em VMs no GCP
---

# Deploy de backend FastAPI e frontend React usando Compute Engine

Esta aula mostra como colocar no ar uma API FastAPI e uma SPA em React usando **Máquinas Virtuais** do Google Cloud. Todo o provisionamento principal é feito pela **console web**, mas incluímos os comandos que você executará dentro das VMs para instalar e publicar os serviços.

## 1. Visão geral da arquitetura

| Componente | VM | Porta | Função |
|------------|----|-------|--------|
| `vm-backend` | e2-medium (Ubuntu 22.04) | 8000/443 | Executa FastAPI + Uvicorn (podendo ficar atrás de Nginx). |
| `vm-frontend` | e2-small (Ubuntu 22.04) | 80/443 | Hospeda build React via Nginx. |
| Firewall | Regras VPC | 22, 80, 443, 8000 | Libera somente as portas necessárias para cada VM. |
| Observabilidade | Cloud Logging/Monitoring (Ops Agent) | - | Envia métricas e logs para o SOC. |

## 5. Configurar o backend FastAPI com Docker

### 5.1 Acessar, atualizar e instalar Docker

```bash
gcloud compute ssh vm-backend --zone=us-central1-a
sudo apt update && sudo apt upgrade -y
sudo apt install docker.io docker-compose-plugin -y
sudo usermod -aG docker $USER
exit # reconecte para aplicar o grupo docker
gcloud compute ssh vm-backend --zone=us-central1-a
```

> Alternativa sem CLI: na console, abra *Compute Engine → VM instances → SSH*. O navegador abre um shell seguro.

### 5.2 Preparar repositório e Dockerfile

```bash
cd /opt
sudo mkdir fastapi && sudo chown $USER:$USER fastapi
cd fastapi
cat <<'EOF' > requirements.txt
fastapi
uvicorn[standard]
EOF

cat <<'EOF' > Dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
ENV PORT=8000
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
EOF

cat <<'EOF' > main.py
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def root():
		return {"status": "ok"}
EOF
```

Traga seu código real via `git clone` ou SCP substituindo `main.py`.

### 5.3 Construir a imagem localmente

```bash
docker build -t fastapi-app:v1 .
```

### 5.4 Executar container e proxy Nginx via Docker

Crie uma rede isolada para os containers:

```bash
docker network create backend-net
```

Execute a API:

```bash
docker run -d --name fastapi \
	--network backend-net \
	--restart unless-stopped \
	fastapi-app:v1
```

Crie a configuração do proxy:

```bash
cat <<'EOF' > nginx-fastapi.conf
server {
		listen 80;
		server_name _;

		location / {
				proxy_pass http://fastapi:8000;
				proxy_set_header Host $host;
				proxy_set_header X-Real-IP $remote_addr;
		}
}
EOF
```

Suba o Nginx em container publicado na mesma rede:

```bash
docker run -d --name nginx-fastapi \
	--network backend-net \
	-p 80:80 \
	-v $(pwd)/nginx-fastapi.conf:/etc/nginx/conf.d/default.conf:ro \
	--restart unless-stopped \
	nginx:1.25-alpine
```

Para HTTPS, crie um HTTP(S) Load Balancer com certificado gerenciado ou rode um container `certbot` para emitir TLS diretamente.

```bash
cat <<'EOF' | sudo tee /etc/systemd/system/fastapi.service
[Unit]
Description=FastAPI app
After=network.target

[Service]
User=www-data
WorkingDirectory=/opt/fastapi
Environment="PATH=/opt/fastapi/.venv/bin"
ExecStart=/opt/fastapi/.venv/bin/uvicorn main:app --host 0.0.0.0 --port 8000
Restart=on-failure

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable --now fastapi
```

### 5.4 Proxy reverso opcional

Configure o Nginx para encaminhar `80/443 → 8000`:

```bash
sudo rm /etc/nginx/sites-enabled/default
cat <<'EOF' | sudo tee /etc/nginx/sites-available/fastapi
server {
	listen 80;
	server_name _;

	location / {
		proxy_pass http://127.0.0.1:8000;
		proxy_set_header Host $host;
		proxy_set_header X-Real-IP $remote_addr;
	}
}
EOF

sudo ln -s /etc/nginx/sites-available/fastapi /etc/nginx/sites-enabled/fastapi
sudo systemctl restart nginx
```

Use o certificado gerenciado do **Certificate Manager** ou `certbot` para ativar HTTPS.

## 6. Configurar o frontend React

### 6.1 Build e imagem Docker

Reaproveite o `Dockerfile` multi-stage do frontend mostrado no guia principal ou crie diretamente na VM:

```bash
cat <<'EOF' > Dockerfile
FROM node:20 AS build
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

FROM nginx:1.25-alpine
COPY --from=build /app/build /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
EOF

docker build -t react-frontend:v1 .
```

### 6.2 Rodar o container na `vm-frontend`

```bash
gcloud compute ssh vm-frontend --zone=us-central1-a
sudo apt update && sudo apt install docker.io -y
sudo usermod -aG docker $USER
exit && gcloud compute ssh vm-frontend --zone=us-central1-a

docker run -d --name frontend \
  -p 80:80 \
  --restart unless-stopped \
	react-frontend:v1
```

> Para HTTPS, crie um HTTP(S) Load Balancer apontando para a `vm-frontend` ou rode o container `nginx` com certificados montados via Secret Manager/`certbot`.

## 7. Observabilidade e manutenção

- Instale o [Ops Agent](https://cloud.google.com/stackdriver/docs/solutions/agents/ops-agent/installation) para enviar métricas/logs.
- Configure alertas no Cloud Monitoring (latência, CPU, uso de disco).
- Crie snapshot dos discos antes de upgrades importantes.

## 8. Checklist final

- [ ] VPC e firewall criados via console com tags corretas.
- [ ] `vm-backend` servindo FastAPI em 8000 e exposto via Nginx/HTTPS.
- [ ] `vm-frontend` servindo build React em Nginx.
- [ ] Certificados TLS aplicados (Load Balancer ou certbot).
- [ ] Logs e métricas integrados ao SOC.

Assim você demonstra o caminho “clássico” de deploy em VMs antes de migrar para serviços gerenciados como Cloud Run ou GKE.
