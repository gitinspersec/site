# Resposta a Incidentes

A **Resposta a Incidentes** é um conjunto de processos e práticas coordenadas que visam **detectar, analisar, conter, erradicar e recuperar sistemas após um incidente de segurança**.  
Baseia-se no guia **NIST Special Publication 800-61 Rev. 2**, referência internacional para criação e operação de **CSIRTs (Computer Security Incident Response Teams)**.

---

## Conceito

A Resposta a Incidentes (RI) é a **disciplina operacional da segurança cibernética** voltada a **minimizar o impacto de ataques** e **restaurar operações de forma controlada e segura**.  
Ela não é apenas reativa — quando bem implementada, torna-se **um mecanismo de aprendizado contínuo** dentro do ciclo de defesa.

---

## Importância

- **Reduz tempo e custo** de recuperação (MTTR).  
- **Preserva evidências forenses** e garante conformidade regulatória (ex: LGPD, GDPR).  
- **Evita propagação de ataques** e perdas maiores.  
- **Fortalece o ciclo de defesa** — incidentes tratados corretamente alimentam melhorias em detecção, hardening e resposta futura.

> **Exemplo de uso real:**  
> Um ransomware criptografa parte da rede corporativa. O CSIRT aciona o *Incident Commander*, isola os sistemas afetados, comunica stakeholders via canal *out-of-band* e executa o *runbook* de recuperação. Após a restauração, realiza-se a análise de causa raiz e revisão das políticas de backup.

---

## Conceitos Fundamentais em Operações de Segurança

Antes de detalhar o processo de **Resposta a Incidentes**, é essencial compreender alguns **termos e tecnologias centrais** usados no contexto de **operações de segurança (SecOps)** e **engenharia de detecção**.  
Esses conceitos formam a base da detecção, análise e resposta a ameaças em ambientes corporativos modernos.

---

### SIEM — *Security Information and Event Management*

O **SIEM** é uma plataforma que **coleta, normaliza, correlaciona e analisa logs de diferentes fontes** (sistemas, redes, aplicações, nuvem).  
Ele gera **alertas e dashboards centralizados** sobre comportamentos suspeitos.

- **Função:** Detecção e correlação de eventos de segurança.  
- **Exemplos:** *Splunk Enterprise Security, IBM QRadar, Microsoft Sentinel, Elastic SIEM.*  
- **Uso prático:** detectar login anômalo em um servidor e correlacionar com uma elevação de privilégio subsequente.

> SIEM é o “coração analítico” do SOC — onde dados viram alertas acionáveis.

---

### SOAR — *Security Orchestration, Automation and Response*

O **SOAR** automatiza e orquestra fluxos de resposta a incidentes, integrando dados do SIEM e de outras ferramentas.  
Ele permite que playbooks (guias de procedimentos) sejam executados automaticamente — como **bloquear um IP, isolar uma máquina ou abrir um ticket**.

- **Função:** Automação de resposta e coordenação de tarefas repetitivas.  
- **Exemplos:** *Cortex XSOAR, Splunk Phantom, Siemplify.*  
- **Uso prático:** ao detectar phishing, o SOAR automaticamente bloqueia o domínio, alerta o usuário e coleta evidências.

> SOAR transforma “alertas” em “ações coordenadas” com mínima intervenção humana.

---

### EDR — *Endpoint Detection and Response*

O **EDR** monitora e protege **dispositivos finais (endpoints)** — como servidores, notebooks e estações de trabalho — contra atividades maliciosas.  
Ele fornece **telemetria detalhada**, isolamento remoto e resposta em tempo real.

- **Função:** Detecção e resposta focadas em endpoints.  
- **Exemplos:** *CrowdStrike Falcon, Microsoft Defender for Endpoint, SentinelOne, Sophos Intercept X.*  
- **Uso prático:** detectar execução de `mimikatz.exe` e isolar automaticamente o host afetado.

> O EDR é a “linha de frente” da resposta — onde a ameaça se manifesta fisicamente.

---

### XDR — *Extended Detection and Response*

O **XDR** expande o conceito de EDR, unificando dados de **endpoints, e-mail, rede, identidade e nuvem**.  
Ele cria uma visão **correlacionada e unificada** do ataque, reduzindo o tempo de investigação.

- **Função:** Detecção e resposta integrada entre múltiplos domínios.  
- **Exemplos:** *Microsoft 365 Defender, Palo Alto Cortex XDR, Trellix, Trend Micro Vision One.*  
- **Uso prático:** correlacionar login suspeito no Azure AD, execução de script PowerShell e tráfego anômalo no firewall.

> XDR é a “evolução do EDR” — mais contexto, menos ruído.

---

### NAC / IAM / CASB / DLP — Camadas Complementares

| Sigla | Significado | Função Principal | Exemplo |
|--------|--------------|------------------|----------|
| **NAC** | *Network Access Control* | Controla quem e o que acessa a rede. | Cisco ISE, FortiNAC |
| **IAM** | *Identity and Access Management* | Gerencia identidades, autenticação e autorização. | Azure AD, Okta |
| **CASB** | *Cloud Access Security Broker* | Controla e audita acesso a serviços em nuvem. | Netskope, McAfee MVISION |
| **DLP** | *Data Loss Prevention* | Monitora e previne vazamento de dados sensíveis. | Symantec DLP, Microsoft Purview DLP |

> Essas tecnologias reforçam o ecossistema de defesa, fornecendo visibilidade e controle sobre identidade, dados e perímetro.

---

### IOC / IOA — *Indicators of Compromise / Attack*

- **IOC:** evidência concreta de um ataque ocorrido (hash, IP, domínio, artefato).  
- **IOA:** indício comportamental de ataque em andamento (execução de script, criação de processo suspeito).  

> Exemplo: `powershell.exe -enc` é um **IOA**, enquanto o hash do malware baixado é um **IOC**.

---

> **Resumo:**  
> Esses conceitos compõem o **vocabulário técnico essencial** da segurança operacional.  
> Entendê-los é fundamental para compreender **como o NIST 800-61 estrutura o ciclo de Resposta a Incidentes** — do alerta inicial à recuperação completa.

---

## Etapas da Resposta a Incidentes

### 1. **Preparação**
Estabelece a base da resposta eficaz:
- Políticas e planos de resposta documentados;  
- Contatos atualizados (equipe técnica, jurídico, comunicação, fornecedores);  
- Runbooks de resposta e *golden images* de recuperação;  
- Canais de comunicação fora da rede comprometida (*out-of-band*).  

> Ferramentas: *Confluence/Notion* (documentação), *Signal/Matrix* (comunicação segura).

---

### 2. **Detecção e Análise**
Identifica e confirma o incidente:
- Correlaciona alertas, logs e evidências;  
- Classifica severidade e escopo;  
- Formula hipóteses iniciais de ataque.  

> Exemplo: “Credential dumping detectado em servidor de domínio.”  
> Ferramentas: *SIEM (Splunk, Sentinel)*, *EDR (CrowdStrike, Defender)*, *Threat Intel Feeds*.

---

### 3. **Contenção e Erradicação**
Ações imediatas para evitar danos adicionais:
- Isolar máquinas e redes comprometidas;  
- Revogar credenciais expostas;  
- Bloquear indicadores (IPs, hashes, domínios);  
- Remover persistência e artefatos maliciosos.  

> Ferramentas: *SOAR (Cortex XSOAR, Splunk Phantom)*, *Firewall/EDR APIs*, *AD Management Scripts*.

---

### 4. **Recuperação**
Restaura operações de forma controlada:
- Reinstala *golden images* limpas;  
- Garante integridade e monitoramento pós-restauração;  
- Avalia **RTO (Recovery Time Objective)** e **RPO (Recovery Point Objective)**.  
- Aplica *hardening* para evitar recorrência.  

> Ferramentas: *Backup Systems (Veeam, Rubrik)*, *Infra as Code (Terraform, Ansible)*, *Patch Management*.

---

### 5. **Pós-incidente**
Fase de aprendizado e melhoria contínua:
- Geração de **relatório de incidente (postmortem)**;  
- **Root Cause Analysis (RCA)** detalhada;  
- Ações de melhoria e atualização de detecções;  
- **Tabletop exercises** para treinar equipes e validar planos.

> Ferramentas: *JIRA / ServiceNow* (gestão de incidentes), *MISP* (threat sharing), *Google Docs / Notion* (relatórios).

---

## Papéis Críticos

| Função | Responsabilidade |
|--------|------------------|
| **Incident Commander** | Coordena toda a resposta, toma decisões estratégicas e garante comunicação eficiente. |
| **Comunicações / Jurídico / PR** | Controla fluxo de informação interna e externa, gerencia riscos legais e reputacionais. |
| **Técnicos de Domínio** | Especialistas em redes, identidade, aplicativos e nuvem — executam as ações práticas de contenção e recuperação. |

> A atuação coordenada entre esses papéis reduz ruído, acelera decisões e mantém a organização sob controle durante crises.

---

## Ferramentas de Apoio à Resposta

| Categoria | Exemplos |
|------------|-----------|
| SIEM / SOAR | Splunk, Microsoft Sentinel, Cortex XSOAR |
| EDR / XDR | CrowdStrike, SentinelOne, Defender for Endpoint |
| Gestão de Incidentes | ServiceNow, Jira, TheHive |
| Forense | Volatility, FTK, Autopsy |
| Comunicação Segura | Signal, Wickr, Mattermost (OOB) |
| Automação / Infra | Ansible, Terraform, GitHub Actions |

---

## Conclusão

A **Resposta a Incidentes**, segundo o NIST 800-61, é mais do que reagir a crises — é **um ciclo de aprendizado e maturidade operacional**.  
Cada incidente bem gerido deve **fortalecer a capacidade defensiva**, aprimorar as detecções e consolidar a cultura de segurança.
  
> “Responder é aprender. Cada incidente tratado é uma oportunidade de tornar o próximo impossível de acontecer.”


