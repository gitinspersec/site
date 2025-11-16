---
title: Guia de Deploy no GCP
---

# Deploy completo (backend + frontend) no Google Cloud Platform

Este guia mostra como colocar no ar um backend containerizado e um frontend estático no GCP usando ferramentas gerenciadas. Tudo é feito pela **console web** para facilitar a visualização.

## 1. Arquitetura de referência

| Componente | Serviço GCP | Função |
|------------|-------------|--------|
| Backend API | Cloud Run | Container HTTP escalável, público via HTTPS.
| Banco (opcional) | Cloud SQL ou Firestore | Persistência gerenciada.
| Frontend SPA | Cloud Storage + Cloud CDN | Hospedagem de arquivos estáticos com cache global.
| Observabilidade | Cloud Logging & Cloud Monitoring | Métricas, logs e alertas.
| Segurança | Identity-Aware Proxy / Cloud Armor (opcionais) | Controle de acesso e WAF.

## 2. Pré-requisitos rápidos

- Projeto configurado e faturamento ativo.
- Código do backend containerizado (Dockerfile) e build do frontend (ex.: `npm run build`).
- `gcloud` opcionalmente para builds, mas o foco aqui é no console.

## 3. Backend no Cloud Run (via console)

1. **Container Registry**: publique sua imagem (Cloud Build ou Artifact Registry). Depois:
	- Console → *Artifact Registry* → *Repositórios* → *Criar* (formato Docker, região mais próxima).
	- Suba a imagem (use `gcloud builds submit` ou pipeline CI).
2. **Criar serviço Cloud Run**:
	- Console → *Cloud Run* → *Criar serviço*.
	- Escolha o repositório/imagem.
	- Plataforma: *Gerenciado*, região: por exemplo `us-central1`.
	- Defina o serviço como *Não autenticado* (se a API for pública) ou mantenha autenticado e use IAP.
	- Ajuste recursos (CPU/memória) e variáveis de ambiente (aba *Variáveis*).
	- Conclua; a URL HTTPS será mostrada.

### Firewall para backend (Console)

Mesmo no Cloud Run, você pode limitar IPs através de **VPC Service Controls** ou, se usar Compute Engine / GKE, configure firewall assim:

1. Console → *VPC Network* → *Firewall rules* → *Create firewall rule*.
2. Nome: `allow-backend-http`.
3. Rede: `default` ou a VPC específica.
4. Prioridade: 1000.
5. Direction: *Ingress*.
6. Targets: *Specified service account* ou *Tags* (ex.: `backend`).
7. Source filter: `IP ranges` → `0.0.0.0/0` (ou restrinja a IPs específicos).
8. Protocols/ports: marque `Specified protocols` → `tcp:80,tcp:443`.
9. Criar. A interface mostrará a regra aplicada imediatamente.

> Se usar Cloud Run, combine com Cloud Armor para listas de IP/Geo; o passo acima é mais comum em Compute Engine/GKE.

## 4. Frontend estático (Storage + CDN)

1. Console → *Cloud Storage* → *Buckets* → *Create*.
	- Nome global único (ex.: `inspersec-frontend`).
	- Região multi ou dual conforme necessidade.
	- Desmarque *Uniform bucket-level access* se quiser ACLs, mas recomendamos manter marcado.
2. Após criar, clique no bucket → aba **Permissions** → adicione `allUsers` com papel *Storage Object Viewer* para torná-lo público (ou use Cloud CDN + Load Balancer para HTTPS próprio).
3. Aba **Configuration** → *Static website hosting* → habilite e defina `index.html` e `404.html`.
4. Faça upload do build (`gsutil rsync` ou arraste arquivos na UI).
5. (Opcional) **Cloud CDN**:
	- Console → *Network services* → *Load balancing* → *Create load balancer* → HTTP(S).
	- Backend: bucket GCS; Frontend: HTTPS com certificado gerenciado.
	- Ative Cloud CDN para cache global e obtenha URL amigável.

## 5. Deploy pipeline sugerido

1. CI executa testes e builda imagem Docker + artefatos frontend.
2. Publica imagem no Artifact Registry e artefatos no Cloud Storage (ou Cloud Build Deploy).
3. Gatilho do Cloud Deploy ou Cloud Build aplica nova revisão do Cloud Run e sincroniza bucket.
4. Observabilidade: configure alertas (p95 latência, 5xx) no Cloud Monitoring.

## 6. Checklist de segurança

- [ ] Habilitar **HTTPS** obrigatório (Cloud Run já fornece; para bucket use Load Balancer + certificado).
- [ ] Revisar firewall rules no console (somente portas necessárias, origem restrita se possível).
- [ ] Registrar logs em Cloud Logging e exportar para o SIEM (Pub/Sub → Splunk/Chronicle).
- [ ] Ativar Cloud Armor / WAF para endpoints públicos sensíveis.
- [ ] Usar secret manager para variáveis sensíveis do backend.

## 7. Referências rápidas

- [Documentação Cloud Run](https://cloud.google.com/run/docs)
- [Hosting estático no Cloud Storage](https://cloud.google.com/storage/docs/hosting-static-website)
- [Firewall VPC via console](https://cloud.google.com/vpc/docs/using-firewalls)
- [Cloud CDN com bucket](https://cloud.google.com/cdn/docs/setting-up-cdn-with-bucket)

Pronto! Com esses passos você consegue expor backend e frontend no GCP usando apenas a interface visual, mantendo boas práticas de segurança e observabilidade.
