# Aula 01 — Fundamentos de Blue Team (Teórica)

> **Objetivo geral:** apresentar a visão sistêmica da defesa cibernética (Blue Team) — princípios, estruturas, processos, ferramentas e métricas — para que o aluno entenda **o que** precisa existir em uma operação defensiva madura e **por que**.

---

## 1) O que é Blue Team

**Blue Team** é o conjunto de pessoas, processos e tecnologias dedicados a **prevenir, detectar, responder e recuperar** de incidentes de segurança.
Seu trabalho se materializa em **controles**, **monitoramento**, **regras de detecção**, **playbooks**, **relatórios** e **melhorias contínuas**.

**Papéis comuns (podem se sobrepor em equipes pequenas):**

* **SOC (Tier 1–3):** monitoramento 24×7, triagem, investigação, escalonamento.
* **Engenharia de detecção:** cria e mantém regras, pipelines de logs, conteúdo no SIEM/EDR, *detections-as-code*.
* **Resposta a Incidentes (IR):** coordena contenção, erradicação, recuperação e *lessons learned*.
* **Threat Hunting:** hipóteses proativas, busca por IOCs/IOAs sem esperar alertas.
* **Forense/Inteligência:** análise de disco/memória/rede, atribuição, *threat intel*.
* **Gestão de vulnerabilidades/ativos:** prioriza correções, mede exposição.
* **GRC:** políticas, conformidade, risco, auditoria.

---

## 2) Princípios de Segurança da Informação

* **CIA Triad:** **Confidencialidade**, **Integridade**, **Disponibilidade**.
* **Parkerian Hexad (amplia a CIA):** + **Autenticidade**, **Utilidade**, **Possessão**.
* **Princípios operacionais:**

  * **Menor Privilégio** e **Necessidade de Saber**.
  * **Segurança por padrão** (default deny).
  * **Segregação de funções** (SoD) e **segregação de ambientes** (dev/test/hom/prod).
  * **Observabilidade by design** (telemetria útil desde o início).
  * **Zero Trust**: verifique explicitamente, aplique menor privilégio, parta do pressuposto de violação.

---

## 3) Modelos mentais do adversário

* **Cyber Kill Chain (Lockheed Martin):** Recon → Armazenamento → Entrega → Exploração → Instalação → C2 → Ações.
  Útil para **quebrar a cadeia** em múltiplos pontos.
* **MITRE ATT\&CK:** catálogo de **táticas** (objetivos do atacante) e **técnicas** (como executa).
  Excelente para **mapear lacunas de detecção** e construir **casos de uso**.
* **MITRE D3FEND:** contramedidas mapeadas a ATT\&CK (referência defensiva).

> **Ideia-chave:** mapeie suas **detecções** e **controles** para as **táticas/técnicas** mais prováveis no seu contexto.

---

## 4) Defesa em Camadas (Defense-in-Depth)

| Camada          | Objetivo                  | Exemplos de Controles                           |
| --------------- | ------------------------- | ----------------------------------------------- |
| Físico          | Proteger o acesso físico  | Sala segura, cofres, CCTV, controle de acesso   |
| Rede            | Conter/segmentar tráfego  | VLANs, firewalls L3/L7, IDS/IPS, NAC, VPN       |
| Endpoint        | Proteger dispositivos     | EDR/AV, hardening, *application control*, patch |
| Identidade      | Confiar com parcimônia    | IAM, MFA, PAM, revisão de acesso, SSO           |
| Aplicação       | Blindar lógica de negócio | WAF, *secure coding*, SAST/DAST, RASP           |
| Dados           | Proteger e recuperar      | Classificação, criptografia, DLP, backup/DR     |
| Observabilidade | Ver e reagir rápido       | SIEM/SOAR, logs, métricas, \* UEBA/NDR          |

> **Anti-padrão:** depender de **um** único controle (ex.: “tem antivírus, logo está seguro”). Camadas compensam falhas.

---

## 5) Arquitetura de Operações de Defesa

### 5.1 Pipeline de Telemetria (alto nível)

1. **Coleta:** endpoints, servidores, rede, identidade, nuvem, aplicações.
2. **Transporte/Normalização:** agentes, Syslog/Beats/Collectors, parsing, *enrichment* (GeoIP, AD, CMDB).
3. **Armazenamento e Retenção:** *hot* (rápido/caro) vs *cold* (lento/barato); retenção por requisitos regulatórios e de investigação.
4. **Detecção:** regras, correlações, ML/UEBA, listas de observação, *risk scoring*.
5. **Orquestração/Automação (SOAR):** playbooks, tickets, integrações (firewall, EDR, IAM).
6. **Observabilidade:** dashboards, SLOs, cobertura de fontes, *health checks*.

### 5.2 Ferramentas (funções e diferenças)

* **SIEM:** correlação, busca, alertas, compliance.
* **EDR/XDR:** telemetria de endpoint, isolamento, *rollback*, resposta.
* **NDR:** visibilidade e anomalias na rede (leste-oeste).
* **SOAR:** automatiza respostas e padroniza playbooks.
* **Vuln/Exposure Mgmt:** descoberta de ativos, varreduras, priorização (CVSS/EPSS), *attack surface*.

---

## 6) Engenharia de Detecção (sem comando, só conceito)

**Ciclo de vida do conteúdo de detecção:**

1. **Requisito/hipótese:** “Como perceberíamos *credential dumping*?”
2. **Mapeamento de dados:** quais logs contêm os campos necessários?
3. **Design da regra:** lógica, janela temporal, agregação, exceções.
4. **Validação/QoS:** *test harness*, datasets de referência, *attack simulation/BAS*.
5. **Deploy versionado:** *detections-as-code*, revisão (*PR*), *feature flags*.
6. **Tuning & manutenção:** falsos positivos/negativos, *drift*, depreciação.
7. **Medição:** precisão/recall, *coverage* por ATT\&CK, impacto em MTTD/MTTR.

> **Boas práticas:** nomenclatura consistente, tags (ATT\&CK, severidade), *owner*, descrição do caso de uso, *runbook* associado.

---

## 7) Resposta a Incidentes (visão NIST 800-61)

* **Preparação:** políticas, contatos, *runbooks*, *golden images*, *out-of-band comms*.
* **Detecção/Análise:** confirmar incidente, classificar severidade, escopo, hipóteses.
* **Contenção/Erradicação:** isolar, bloquear, revogar credenciais, remover persistência.
* **Recuperação:** restaurar serviços com segurança (RTO/RPO), *hardening pós-incidente*.
* **Pós-incidente:** relatório, *root cause analysis*, ações de melhoria, *tabletop exercises*.

**Papéis críticos:** *Incident Commander*, comunicações (inclui jurídico/PR), responsáveis técnicos por domínio (rede, identidade, app, nuvem).

---

## 8) Inteligência de Ameaças (Threat Intel)

* **Níveis:** estratégico (tendências), operacional (campanhas), tático (TTPs/IOAs), técnico (IOCs).
* **Uso prático:**

  * enriquecer alertas (ex.: reputação de IP/domínio),
  * ajustar priorização (ex.: campanhas ativas no seu setor),
  * abastecer *hunting* e validação de hipóteses.
* **Cautela:** IOCs têm vida curta; foque em **comportamentos** (IOAs/TTPs).

---

## 9) Gestão de Vulnerabilidades e Exposição

* **Inventário de ativos** é base de tudo (on-prem, nuvem, shadow IT).
* **Ciclo:** descoberta → avaliação → priorização → remediação → verificação.
* **Priorizar por risco real:** criticidade do ativo, exploração ativa, compensações existentes, **EPSS** (prob. de exploração) além do **CVSS**.
* **Integração com mudança (Change Mgmt):** janelas, rollback, evidências.

---

## 10) Governança, Risco e Conformidade (GRC)

* **Políticas e padrões:** senhas/MFA, e-mail, classificação de dados, *secure coding*, *logging baseline*, *hardening* (CIS Benchmarks).
* **Risco:** identificar, analisar (probabilidade × impacto), tratar (aceitar, mitigar, transferir, evitar).
* **Conformidade e privacidade:** requisitos de retenção de logs, consentimento/necessidade, minimização de dados.
* **Continuidade (BCP/DR):** metas de **RTO/RPO**, testes de restauração, estratégia **3-2-1-1-0** para backups (3 cópias, 2 mídias, 1 off-site, 1 imutável, 0 erros no teste).

> **Dica:** políticas simples, aplicáveis e auditáveis funcionam melhor do que documentos extensos ignorados na prática.

---

## 11) Estratégia de Logs (sem comandos)

* **Comece por onde dói mais:** identidade (AD/IdP), endpoint (EDR/SO), perímetro (FW/WAF), e-mail, DNS, *cloud control plane*.
* **Qualidade > quantidade:** campos úteis, *timestamps* corretos, *hostnames* canônicos, correlação com CMDB/Asset-ID.
* **Retenção:** balancear custo, investigação, requisitos regulatórios.
* **Saúde do pipeline:** monitore *drop rates*, *lag*, perdas de agentes, ingestões anômalas.

---

## 12) Métricas que importam

* **Eficiência de detecção:** *coverage* ATT\&CK por técnica crítica, precisão/recall por caso de uso.
* **Velocidade:** **MTTD** (tempo para detectar), **MTTR** (tempo para resolver), **dwell time** (tempo de permanência).
* **Qualidade operacional:** taxa de falsos positivos, *mean time to acknowledge*, backlog por severidade, *noise-to-signal ratio*.
* **Maturidade:** automações ativas, *hunting* periódico, exercícios (*tabletops*), *post-mortems* com ações concluídas.

---

## 13) Pessoas e Processos

* **Treinamento contínuo:** rotação em *on-call*, *runbooks* vivos, *brown bags*.
* **Turnover & fadiga de alerta:** *shift hygiene*, silencia ruídos, revisa SLAs e limiares.
* **Purple Team:** colaboração Red ↔ Blue para validar controles e detecções.
* **Comunicação:** relatórios executivos (negócio), relatórios técnicos (engenharia), *status pages* (operação).

---

## 14) Anti-padrões comuns (para evitar)

* “Compra uma ferramenta e resolve tudo.” → **Ferramenta sem processo é shelfware.**
* “Vamos logar tudo para sempre.” → **Custo e ruído inviabilizam a operação.**
* “MFA em todo lugar é suficiente.” → **Ótimo controle, não é panaceia.**
* “Detecção pronta de vendor basta.” → **Contexto local e *tuning* são essenciais.**
* “Backups existem, então estamos seguros.” → **Teste de restauração regular é o que conta.**

---

## 15) Roteiro de primeiros 90 dias (exemplo conceitual)

1. **Mapeie o que existe:** ativos críticos, identidades, superfícies expostas, *crown jewels*.
2. **Estabeleça o mínimo viável:**

   * MFA para acessos críticos,
   * inventário confiável,
   * coleta de logs de identidade/endpoint/perímetro,
   * regras de detecção para técnicas de maior probabilidade/impacto.
3. **Padronize processos:** *incident intake*, severidade, *on-call*, *runbooks*.
4. **Feche o ciclo:** *post-incident reviews*, métricas (MTTD/MTTR), *backlog grooming* de detecções.
5. **Planeje evolução:** automações SOAR, *hunting* orientado a ATT\&CK, testes de DR e *tabletops* trimestrais.

---

## 16) Glossário Essencial

* **IOC/IOA:** Indicador de Comprometimento / de Atividade.
* **UEBA:** Análise de comportamento de usuário/entidade.
* **PAM:** *Privileged Access Management*.
* **CMDB:** Base de dados de configuração (ativos/relacionamentos).
* **EPSS:** *Exploit Prediction Scoring System* (probabilidade de exploração).
* **RTO/RPO:** Objetivos de tempo de recuperação / ponto de recuperação.
* **BAS:** *Breach and Attack Simulation* (simulação contínua).

---

## 17) Leituras e referências (para estudo)

* **NIST CSF** (framework de funções: Identify, Protect, Detect, Respond, Recover).
* **NIST SP 800-61** (guia de resposta a incidentes).
* **MITRE ATT\&CK / D3FEND** (táticas/técnicas e contramedidas).
* **CIS Controls v8** e **CIS Benchmarks** (controles priorizados e *hardening*).
* **ISO/IEC 27001/27002** (gestão e controles de segurança).

> Use as referências como **catálogos** para construir seus **casos de uso** e **políticas**; evite copiar “no escuro”.
