# Aula 05 — Arquitetura de Cloud para Redes e Segurança

> Objetivo: sair do “zero” em cloud networking, entender os blocos essenciais (VPC, sub-rede, rota, firewall) e fechar com a visão de cibersegurança na nuvem.

## 1. Começando do básico

### 1.1 O que é “nuvem” em poucas palavras

- **IaaS (Infrastructure as a Service):** você aluga servidores, redes e armazenamento, mas configura tudo.
- **PaaS e SaaS:** serviços mais prontos (bancos gerenciados, e-mail, CRM). Conhecer o modelo importa porque define quem protege o quê (shared responsibility).

### 1.2 Como pensar em redes na nuvem

- Troque “data center físico” por **VPC/VNet/VPC Network**: blocos IP isolados dentro de AWS, Azure e GCP.
- Dentro da rede virtual criamos **sub-redes** (CIDRs) como fazemos com VLANs.
- Conectamos tudo com **route tables** (regras que dizem para onde vai cada destino).

| Conceito | AWS | Azure | GCP |
|----------|-----|-------|-----|
| Rede virtual | VPC | VNet | VPC Network |
| Sub-rede | Subnet | Subnet | Subnet |
| Firewall estadoful | Security Group | NSG | Firewall Rules |
| Logs de fluxo | VPC Flow Logs | NSG Flow Logs | VPC Flow Logs |

> Quer ver isso na prática? Confira a aula complementar [`GCP/VPC`](./gcp/vpc_gcp.md) com um laboratório passo a passo no Google Cloud Platform.

!!! tip
	Antes de criar qualquer recurso, desenhe o diagrama com blocos IP e anote quem precisa falar com quem. Evita retrabalho e conflitos.

## 2. Construindo a rede virtual

| Bloco | AWS | Azure | GCP | Analogia no data center |
|-------|-----|-------|-----|-------------------------|
| Rede virtual | VPC | VNet | VPC Network | Campus / data center |
| Tabela de rotas | Route Table | Route Table | Route Table | Tabela de roteador |
| Sub-rede | Subnet | Subnet | Subnet | VLAN |
| Saída pública | Internet Gateway | Internet Gateway | Internet Gateway | Link dedicado / firewall de borda |
| Saída privada | NAT Gateway | NAT Gateway | Cloud NAT | Proxy/NAT corporativo |

Passos típicos:

1. Escolher um bloco CIDR amplo (ex.: `10.50.0.0/16`).
2. Dividir em sub-redes públicas (com acesso de entrada controlado) e privadas (somente saída via NAT).
3. Associar route tables: tráfego interno vai direto, tráfego para Internet sai pelo gateway apropriado.

## 3. Segurança e segmentação

### 3.1 NACL x Security Group

| Característica | NACL | Security Group |
|----------------|------|----------------|
| Onde atua | Sub-rede | Interface/instância |
| Estado | Stateless (precisa permitir ida e volta) | Stateful (resposta liberada automaticamente) |
| Uso típico | Filtro grosso na borda da sub-rede | Firewall fino por aplicação |

Boas práticas:

- Use NACLs para bloquear tráfego totalmente proibido (ex.: portas administrativas).
- Use Security Groups com nomes claros (SG-Web, SG-DB) e limite-os ao mínimo necessário.
- Versione regras em Git/Terraform para saber quem alterou o que.

### 3.2 Segmentação e Zero Trust

- Separe ambientes (prod, dev, teste) em VPCs ou sub-redes distintas.
- Não confie em “rede interna segura”. Trate cada salto como potencialmente hostil.
- Registre e monitore todos os acessos (CloudTrail, Azure Activity Logs, VPC Flow Logs).

## 4. Conectividade além da VPC

| Cenário | Solução | Quando usar |
|---------|---------|-------------|
| Rede ↔ rede (mesmo provedor) | VPC Peering (AWS/GCP), VNet Peering (Azure) | Poucas conexões, baixa complexidade |
| Muitas redes/contas | Transit Gateway (AWS), Virtual WAN Hub (Azure), Cloud Router + VPC Peering (GCP) | Precisa centralizar roteamento |
| Empresa ↔ nuvem | VPN site-to-site (IPsec) | Rápido, custo baixo |
| Baixa latência / alta banda | Direct Connect (AWS), ExpressRoute (Azure), Cloud Interconnect (GCP) | Workloads críticos, replicação |

Checklist para ambientes híbridos:

1. Confirme que os blocos IP não se sobrepõem.
2. Planeje redundância (duas VPNs, duas zonas).
3. Monitore métricas (latência, disponibilidade) e configure alertas para quedas de túnel.

## 5. Contêineres? Só depois

Redes de contêineres (Kubernetes, CNI, Network Policies) merecem uma aula exclusiva. Por enquanto, entenda que:

- Cada pod recebe um IP próprio.
- Services e ingressos agem como balanceadores.
- As mesmas ideias de segmentação se aplicam, só que com ferramentas diferentes (Network Policies, service mesh).

## 6. Cloud + redes + cibersegurança

### 6.1 Shared Responsibility aplicada à rede

- **Provedor:** garante a segurança da nuvem (física, hipervisor, backbone).
- **Cliente:** protege o que está na nuvem (configurações de rede, senhas, chaves, logs).

### 6.2 Principais riscos de rede em cloud

| Risco | Como aparece | Mitigação |
|-------|--------------|-----------|
| Porta aberta ao mundo | SG com `0.0.0.0/0` liberando SSH/RDP | Restringir origem, usar bastion + MFA |
| Falta de logs | Flow Logs desativados | Habilitar logs e enviar ao SIEM |
| Segredo exposto | Chaves/credenciais em instâncias | Usar serviços de cofres (AWS KMS/Secrets Manager, Azure Key Vault) |
| Escala rápida de ataque | Recursos públicos mal configurados | Automação de detecção (Config, Defender, Security Hub) |

### 6.3 Instrumentação para o SOC

- **Flow Logs:** VPC/NSG Flow Logs (AWS/Azure/GCP) mostram quem falou com quem (IP, porta, ação allow/deny).
- **CloudTrail / Activity Logs / Cloud Audit Logs:** auditam mudanças em SGs, NACLs, VPCs.
- **GuardDuty / Defender for Cloud / Security Command Center:** detectam comportamentos suspeitos (port scanning, criptomoedas).
- Integre tudo no SIEM e crie playbooks (SOAR) para isolar instâncias, revogar chaves, ajustar regras automaticamente.

### 6.4 Procedimento mínimo de resposta

1. Identificar recurso afetado (instância, SG, VPC).
2. Isolar: mover para SG de quarentena ou remover regra permissiva.
3. Coletar evidências (logs, snapshots, estado da rota).
4. Corrigir configuração raiz (terraform/apply) e documentar lições aprendidas.

## 7. Resumo visual (mental)

1. **Desenhe a VPC** → sub-redes públicas/privadas → rotas.
2. **Aplique filtros** → NACL (macro) + SG (micro).
3. **Conecte com responsabilidade** → VPN ou Direct Connect.
4. **Observe e reaja** → Flow Logs, CloudTrail, automação.

> Conecte com as aulas anteriores: se entendeu VLAN, roteamento e firewalls físicos, você já tem 70% do caminho. A nuvem apenas virtualiza essas peças e adiciona APIs para gerenciar tudo em poucos cliques ou scripts. Para aprofundar, leia a aula prática de GCP (`aula5/gcp/vpc_gcp.md`) e replique o desenho em AWS e Azure.

## Referências rápidas

- AWS Well-Architected Framework — Pilar de Segurança.
- Azure Architecture Center — Redes e Conectividade.
- Google Cloud Architecture Framework — Área de Segurança.
- [Shared Responsibility Model](https://aws.amazon.com/compliance/shared-responsibility-model/).
- Ferramentas: Terraform, AWS CDK, Azure Bicep (infra como código para garantir consistência).
