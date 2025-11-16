# Aula prática: rede segura no Google Cloud Platform

## 1. Objetivos

- Construir uma topologia mínima em GCP com **VPC**, sub-redes públicas/privadas e NAT.
- Aplicar **firewalls**, **Cloud NAT** e **Cloud Router** para controlar tráfego.
- Integrar **Cloud Logging / Cloud Monitoring** para dar visibilidade ao SOC.
- Preparar a arquitetura para estender o desenho a AWS/Azure mudando apenas nomes.

## 2. Pré-requisitos

- Conta GCP com permissão de `Project Editor` ou superior.
- Projeto selecionado (`gcloud config set project <ID>`).
- SDK instalado localmente **ou** acesso ao Cloud Shell.
- Aproveite os **US$300 em créditos gratuitos por 90 dias** oferecidos ao criar a conta: use-os para praticar sem custos (lembre-se de encerrar os recursos depois).

## 3. Passo a passo

### 3.1 Crie a VPC e sub-redes

```powershell
# VPC customizada
$project="<seu-projeto>"
gcloud compute networks create blue-team-vpc `
  --project=$project `
  --subnet-mode=custom `
  --bgp-routing-mode=regional

# Sub-rede pública (web)
gcloud compute networks subnets create web-subnet `
  --project=$project `
  --network=blue-team-vpc `
  --range=10.10.10.0/24 `
  --region=us-central1

# Sub-rede privada (app)
gcloud compute networks subnets create app-subnet `
  --project=$project `
  --network=blue-team-vpc `
  --range=10.10.20.0/24 `
  --region=us-central1
```

### 3.2 Firewalls estadoful

| Regra | Direção | Ação | Descrição |
|-------|---------|------|-----------|
| `allow-ssh-admin` | Ingress | Allow | Permite SSH para bastion (restrinja IP de origem). |
| `allow-http-web` | Ingress | Allow | Libera portas 80/443 somente para VMs da sub-rede pública. |
| `deny-all-default` | Ingress/Egress | Deny | Mantém padrão de negar o que não for explicitamente liberado. |

```powershell
# Exemplo de firewall HTTP
$sourceRange="0.0.0.0/0"
gcloud compute firewall-rules create allow-http-web `
  --network=blue-team-vpc `
  --direction=INGRESS `
  --action=ALLOW `
  --rules=tcp:80,tcp:443 `
  --target-tags=web `
  --source-ranges=$sourceRange
```

### 3.3 NAT para workloads privados

```powershell
# Cloud Router (necessário para NAT e VPN)
gcloud compute routers create blue-router `
  --network=blue-team-vpc `
  --region=us-central1

# Cloud NAT
gcloud compute routers nats create blue-nat ` 
  --router=blue-router `
  --router-region=us-central1 `
  --nat-all-subnet-ip-ranges `
  --auto-allocate-nat-external-ips
```

> Resultado: workloads na sub-rede `app-subnet` acessam Internet para updates sem expor IP público.

### 3.4 Bastion + instâncias

1. Crie uma VM **bastion** na sub-rede pública com tag `web` e chave SSH gerenciada pelo IAM.
2. Crie VMs na sub-rede privada **sem IP público**, usando tag `app`.
3. Ajuste firewall para permitir SSH somente do bastion (`--source-tags=bastion`).

### 3.5 Observabilidade e SOC

| Componente | Configuração |
|------------|--------------|
| **VPC Flow Logs** | Ative nas sub-redes e envie para Cloud Logging (permite pivô por IP/porta). |
| **Cloud Logging Sink** | Encaminhe logs para BigQuery ou Pub/Sub → SIEM externo. |
| **Cloud Monitoring Alert** | Crie política para tráfego anômalo (ex: >100 conexões TCP/min). |

```powershell
# Ativar Flow Logs na sub-rede privada
gcloud compute networks subnets update app-subnet `
  --region=us-central1 `
  --enable-flow-logs
```

## 4. Extensões rápidas

- **VPN site-to-site**: adicione Cloud VPN conectado ao firewall físico on-premise.
- **Cloud Armor**: proteja o front-end HTTP com WAF gerenciado.
- **Cloud IDS**: inspecione tráfego leste-oeste. Combine alertas com MITRE ATT&CK.

## 5. Checklist final

- [ ] VPC custom com CIDR planejado.
- [ ] Sub-redes com Flow Logs habilitados.
- [ ] Firewalls aplicando princípio do menor privilégio.
- [ ] Cloud NAT + Router garantindo saída controlada.
- [ ] Logs exportados para o SIEM do time Blue Team.

> Termine replicando o mesmo desenho em AWS (VPC + Subnets + NAT Gateway) e Azure (VNet + Subnets + NAT Gateway). A lógica é idêntica; só mudam os nomes.
