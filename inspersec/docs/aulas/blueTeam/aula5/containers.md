# Aula 05 — Redes em Contêineres

> Objetivo: entender como contêineres (Docker, Kubernetes, etc.) usam rede, quais controles estão disponíveis e como isso impacta a segurança do Blue Team.

## 1. Conceitos básicos

| Conceito | Explicação simples | Analogia |
|----------|-------------------|----------|
| Contêiner | Processo isolado com tudo que precisa para rodar | Mini VM muito leve |
| Imagem | Receita imutável do contêiner | Snapshot congelado |
| Host | Máquina (física ou VM) que roda os contêineres | Servidor do data center |
| Engine | Software que gerencia contêiner (Docker, containerd) | Hipervisor para VMs |

### 1.1 Diferença para VM

- VM tem um sistema operacional completo por instância.
- Contêiner compartilha kernel do host; só leva o necessário (binários, libs, app).
- Resultado: inicia em segundos, ocupa poucos MB e permite empacotar microserviços.

## 2. Redes em contêineres (Docker)

### 2.1 Tipos de rede padrão

| Tipo | Como funciona | Quando usar |
|------|----------------|-------------|
| Bridge (padrão) | Cria uma rede virtual isolada no host; contêineres ganham IP próprio | Cenários locais, labs |
| Host | Contêiner compartilha IP/porta do host | Aplicações que precisam de baixa latência e acesso total |
| None | Sem rede (isolamento total) | Jobs batch, análise offline |
| Overlay | Conecta contêineres em hosts diferentes (Swarm) | Clusters distribuídos |

### 2.2 Comunicação básica

- Contêiner → contêiner na mesma rede: usa IP privado (ex.: `172.18.0.x`).
- Expor serviço para fora: mapear porta (`docker run -p 8080:80`).
- DNS interno resolve nomes dos contêineres dentro da rede bridge.

!!! tip "Debug rápido"
	Use `docker network inspect` para conferir endereços e rotas antes de liberar o serviço.

---

## 3. Kubernetes: visão rápida

> Detalhes profundos ficam para outra aula; aqui focamos nos blocos indispensáveis.

### 3.1 Objetos principais

- **Pod:** menor unidade (1 ou mais contêineres) com IP próprio.
- **Node:** host físico/virtual que roda pods.
- **Service:** fornece IP estável e load balance para pods.
- **Ingress:** expõe serviços HTTP/HTTPS usando regras L7.

### 3.2 Plugins CNI

O Kubernetes delega rede a plugins (Calico, Cilium, Flannel). Eles criam as interfaces, atribuem IPs e programam rotas/iptables.

### 3.3 Network Policies

- Funcionam como “firewalls” entre pods/namespaces.
- Regra básica: negar tudo e abrir apenas o necessário (default deny + allow explícito).
- Podem filtrar por labels (ex.: só pods `role=db` aceitam tráfego de `role=api`).

---

## 4. Pipeline de tráfego simplificado

1. Usuário acessa `app.inspersec.com` → ingress/Load Balancer na nuvem.
2. Ingress distribui para um **Service** (ClusterIP ou NodePort).
3. Service encaminha para os pods saudáveis.
4. Network Policies decidem se o tráfego é permitido.

---

## 5. Segurança em ambientes de contêineres

### 5.1 Camadas de defesa

| Camada | Controles | Observações |
|--------|-----------|-------------|
| Build | Imagens oficiais, scan (Trivy/Grype), SBOM | Evite imagens “latest” desconhecidas |
| Runtime | Least privilege, rootless containers, seccomp/AppArmor | Reduza capacidades desnecessárias |
| Rede | Network Policies, Service Mesh (mTLS), firewalls no cluster | Monitorar DNS interno e tráfego leste-oeste |
| Plataforma | RBAC, audit logs, secrets manager | Não guarde segredos em ConfigMaps |

### 5.2 Indicadores para o SOC

- Falha em aplicar políticas (pod fica em namespace sem network policy).
- Tráfego inesperado entre namespaces (ex.: marketing acessando banco).
- Pods abrindo portas para Internet sem autorização.
- Uso de ferramentas como `socat`, `curl` em pods que não deveriam.

### 5.3 Playbook mínimo de resposta

1. Identificar pod/namespaces envolvidos.
2. Isolar (aplicar network policy de quarentena ou escalar replicaset para zero).
3. Coletar logs (`kubectl logs`, eventos, audit). Capturar imagem/FS se necessário.
4. Corrigir manifesto (YAML) e reimplantar. Automatize com GitOps para evitar drift.

---

## 6. Relação com aulas anteriores

- A lógica de VLAN ↔ Network Policy é direta: ambos segmentam quem pode falar com quem.
- Route tables da VPC continuam válidas; o cluster vive dentro de uma sub-rede.
- Security Groups/NACLs protegem a borda; Network Policies cuidam do “miolo”.

---
