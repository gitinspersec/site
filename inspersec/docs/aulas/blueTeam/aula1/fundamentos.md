
# Aula 01 — Fundamentos de Blue Team

> Objetivo: apresentar de forma clara e aprofundada os principais conceitos, estruturas e práticas do Blue Team, explicando termos, exemplos e referências para estudo e aplicação prática.

---

## 1. O que é Blue Team

O termo "Blue Team" refere-se ao grupo responsável pela defesa cibernética de uma organização. O Blue Team é formado por pessoas, processos e tecnologias que trabalham juntos para prevenir, detectar, responder e recuperar de incidentes de segurança. O objetivo é garantir a continuidade dos negócios, proteger dados e ativos, e melhorar continuamente a postura de segurança.

Entre as funções do Blue Team, destacam-se:

**Security Operation Centers (SOCs)** — Centros de Operações de Segurança, que podem ser divididos em diferentes níveis (Tiers 1, 2 e 3). O Tier 1 realiza o monitoramento inicial e triagem de alertas, o Tier 2 aprofunda investigações e responde a incidentes mais complexos, e o Tier 3 lida com ameaças avançadas e análise forense. É importante diferenciar este SOC (Security Operation Center) de outros usos da sigla, como System on Chip, pois aqui tratamos exclusivamente do contexto de segurança cibernética.

**Engenharia de Detecção** — Profissionais que desenvolvem e mantêm regras de detecção, pipelines de logs e conteúdo em plataformas como SIEM (Security Information and Event Management) e EDR (Endpoint Detection and Response). Eles aplicam o conceito de "detections-as-code", tratando regras de detecção como código versionado.

**Resposta a Incidentes (IR)** — Equipe responsável por coordenar a contenção, erradicação e recuperação de incidentes, além de promover o aprendizado pós-incidente (lessons learned).

**Threat Hunting** — Atividade proativa de busca por indicadores de comprometimento (IOCs) e indicadores de atividade (IOAs) sem depender apenas de alertas automáticos. O objetivo é encontrar ameaças que passaram despercebidas pelos controles tradicionais.

**Forense e Inteligência** — Análise detalhada de discos, memória e tráfego de rede para entender a origem e o impacto de incidentes, além de produzir inteligência sobre ameaças (threat intelligence).

**Gestão de Vulnerabilidades e Ativos** — Processo de identificar, priorizar e corrigir vulnerabilidades em sistemas e inventariar ativos críticos.

**GRC (Governança, Risco e Conformidade)** — Área que define políticas, avalia riscos e garante conformidade com normas e legislações.

<br>


**Referências:**
- [IBM: O que é Blue Team?](https://www.ibm.com/topics/blue-team)
- [Microsoft: Blue Team vs Red Team](https://www.microsoft.com/en-us/security/business/security-101/what-is-blue-team)

---

## 2. Princípios de Segurança da Informação

Os princípios de segurança da informação são a base para todas as decisões e controles em uma operação defensiva. O modelo mais conhecido é a **Tríade CIA**:

- **Confidencialidade:** garantir que apenas pessoas autorizadas tenham acesso à informação.
- **Integridade:** assegurar que a informação não seja alterada de forma não autorizada.
- **Disponibilidade:** garantir que a informação esteja acessível quando necessário.

O modelo **Parkerian Hexad** amplia a tríade CIA, incluindo:
- **Autenticidade:** garantir que a informação é genuína.
- **Utilidade:** a informação deve ser útil para quem precisa.
- **Possessão:** controle físico ou lógico sobre a informação.

Além desses, destacam-se princípios operacionais:
- **Menor Privilégio:** cada usuário ou sistema deve ter apenas as permissões estritamente necessárias.
- **Necessidade de Saber:** acesso concedido apenas a quem realmente precisa.
- **Segurança por padrão (default deny):** sistemas devem ser projetados para negar acesso, exceto quando explicitamente permitido.
- **Segregação de funções (SoD):** dividir responsabilidades para evitar conflitos de interesse e fraudes.
- **Observabilidade by design:** projetar sistemas para gerar telemetria útil desde o início.
- **Zero Trust:** nunca confiar automaticamente em nada, mesmo dentro da rede — sempre verificar e validar.

Esses princípios orientam a arquitetura, a escolha de ferramentas e a priorização de controles.


**Referências:**
- [RFC 2196 - Site Security Handbook](https://datatracker.ietf.org/doc/html/rfc2196)
- [NIST SP 800-12: An Introduction to Computer Security PDF](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-12r1.pdf)

---

## 3. Modelos mentais do adversário

Para defender bem, é fundamental entender como os atacantes pensam e estruturam suas ações. Dois modelos amplamente usados são o **Cyber Kill Chain** e o **MITRE ATT&CK**. Eles possuem abordagens diferentes e se complementam na prática.

---

### 3.1 Cyber Kill Chain

O **Cyber Kill Chain**, criado pela Lockheed Martin em 2011, descreve o ataque cibernético como uma **cadeia sequencial de 7 etapas**. A ideia central é que, se você conseguir interromper o ataque em qualquer uma dessas etapas, a cadeia se quebra e o ataque falha.

**Etapas da Kill Chain:**

1. **Reconhecimento (Reconnaissance):** o atacante coleta informações sobre o alvo — domínios, IPs públicos, funcionários no LinkedIn, tecnologias usadas (ex: servidores expostos no Shodan). Pense nisso como o atacante estudando a porta antes de tentar abri-la.

2. **Arsenalização (Weaponization):** o atacante prepara a "arma" — um documento Office com macro maliciosa, um exploit kit, ou um payload customizado. Esta etapa geralmente ocorre **fora da rede** do alvo, então é difícil de detectar diretamente, mas é possível obter inteligência sobre kits conhecidos (ex: ransomware builders).

3. **Entrega (Delivery):** a arma chega ao alvo — via e-mail de phishing, link malicioso, drive-by download (site comprometido que infecta visitantes), ou até pen drive deixado no estacionamento. Aqui, controles como filtros de e-mail, proxies e treinamento de usuários são cruciais.

4. **Exploração (Exploitation):** o código malicioso é executado — a macro roda, a vulnerabilidade é explorada, o usuário clica no link. É o momento em que o atacante "entra" no sistema. Exemplo: CVE-2017-0199 permitia que documentos RTF executassem código apenas ao serem abertos.

5. **Instalação (Installation):** o atacante estabelece **persistência** (capacidade de sobreviver a reinicializações) — cria uma chave no registro do Windows, instala um serviço malicioso, ou adiciona um cronjob no Linux. Exemplo: malware cria `HKCU\Software\Microsoft\Windows\CurrentVersion\Run` para iniciar com o sistema.

6. **Comando e Controle (C2, Command and Control):** o malware "liga de volta" para o servidor do atacante, estabelecendo comunicação contínua. Pode usar HTTP/HTTPS, DNS, ou protocolos customizados. Exemplo: beaconing periódico a cada 10 minutos para receber instruções. Este é um dos pontos mais ricos para detecção (DNS incomum, conexões para IPs suspeitos, padrões de tráfego regulares).

7. **Ações sobre o objetivo (Actions on Objectives):** o atacante finalmente executa seu objetivo — roubar dados (exfiltração), criptografar arquivos (ransomware), sabotar sistemas, ou espionar. Pode incluir movimento lateral (pular para outros computadores na rede), escalada de privilégios (virar administrador), e descoberta de ativos críticos.

> **Nota rápida:** *C2 (Command and Control)* é como o "telefone secreto" entre o atacante e o computador infectado. O malware regularmente "chama" o servidor do atacante para pedir instruções, como um agente secreto reportando para a central.

**Limitações da Kill Chain no contexto moderno:**

- **Não cobre movimento lateral explicitamente:** em ambientes complexos (Active Directory, cloud híbrida), o atacante raramente para após o primeiro host comprometido. Ele pivota internamente, rouba credenciais, e espalha-se — a Kill Chain original trata isso apenas como "Ações sobre o objetivo".
- **Foco em malware tradicional:** ataques modernos frequentemente abusam de ferramentas legítimas (LOLBins — Living Off the Land Binaries, como PowerShell, WMI, PsExec) e identidade (credenciais roubadas, OAuth malicioso), sem "arsenalizar" um payload clássico.
- **Cloud e SaaS:** em ataques diretos a APIs ou aplicações SaaS (ex: comprometer conta Office 365 via credential stuffing), várias etapas da Kill Chain simplesmente não se aplicam.

Apesar disso, a Kill Chain continua valiosa como narrativa de ataque e para comunicação com gestores e não-técnicos, pois sua linearidade é intuitiva.

---

### 3.2 MITRE ATT&CK e D3FEND

O **MITRE ATT&CK** (Adversarial Tactics, Techniques, and Common Knowledge) é um **framework não-linear** que organiza o conhecimento sobre ataques reais em uma matriz de **táticas** (o *porquê* — objetivos do atacante) e **técnicas** (o *como* — métodos usados).

**Diferença fundamental:**
- Kill Chain é sequencial: Reconhecimento → Entrega → Exploração → ...
- ATT&CK é matricial: o atacante pode usar várias técnicas em paralelo, revisitar táticas, ou pular etapas.

**Principais táticas do ATT&CK (Enterprise):**

1. **Reconnaissance (Reconhecimento):** coleta externa de informações.
2. **Resource Development (Desenvolvimento de Recursos):** preparação de infraestrutura (domínios, servidores C2, contas comprometidas).
3. **Initial Access (Acesso Inicial):** como o atacante entra — phishing, exploits públicos, credenciais válidas, supply chain.
4. **Execution (Execução):** como o código roda — scripts, binários, componentes do SO.
5. **Persistence (Persistência):** como o atacante mantém acesso — chaves de registro, serviços, contas backdoor.
6. **Privilege Escalation (Escalada de Privilégios):** como o atacante se torna admin/root.
7. **Defense Evasion (Evasão de Defesas):** como o atacante esconde atividade — desabilitar antivírus, obfuscar código, mascarar processos.
8. **Credential Access (Acesso a Credenciais):** roubo de senhas — dump de LSASS, keylogging, ataques a Kerberos.
9. **Discovery (Descoberta):** mapear a rede, usuários, processos, compartilhamentos.
10. **Lateral Movement (Movimento Lateral):** pular para outros sistemas — Pass-the-Hash, RDP, WMI remoto.
11. **Collection (Coleta):** juntar dados de interesse — screenshots, arquivos, e-mails.
12. **Command and Control (C2):** canais de comunicação com servidores externos.
13. **Exfiltration (Exfiltração):** roubar dados para fora — via HTTP, FTP, DNS tunneling, cloud storage.
14. **Impact (Impacto):** causar danos — ransomware, destruição de dados, sabotagem.

> **Nota rápida:** *Lateral Movement* é quando o atacante, já dentro da rede, "pula" de um computador comprometido para outro, espalhando-se internamente como um vírus em uma escola.

**Como usar o ATT&CK:**

- **Mapear cobertura de detecção:** sua organização detecta Pass-the-Hash? Detecta PowerShell Empire C2? Use a ferramenta [ATT&CK Navigator](https://mitre-attack.github.io/attack-navigator/) para visualizar lacunas.
- **Priorizar casos de uso:** analise incidentes passados ou threat intelligence relevante ao seu setor. Quais técnicas são mais usadas contra você? Priorizá-las.
- **Threat Hunting:** construir hipóteses baseadas em técnicas — "vamos caçar evidências de T1003.001 (LSASS Memory Dump) nos últimos 30 dias".
- **Purple Team:** combinar Red Team (simular ataques) com Blue Team (testar detecções) usando técnicas ATT&CK específicas.

**MITRE D3FEND:**

O **D3FEND** é o complemento defensivo do ATT&CK. Ele cataloga **contramedidas** (ex: Application Hardening, Credential Hardening, Network Traffic Filtering) e as mapeia às técnicas ofensivas. É útil para escolher controles e justificar investimentos.

Exemplo: contra T1003.001 (dump de LSASS), D3FEND sugere "Credential Access Protection" (ex: Windows Defender Credential Guard), "Process Segment Execution Prevention", e monitoramento de acessos ao processo lsass.exe.

**Referências:**
- [MITRE ATT&CK](https://attack.mitre.org/)
- [MITRE ATT&CK Navigator](https://mitre-attack.github.io/attack-navigator/)
- [MITRE D3FEND](https://d3fend.mitre.org/)
- [NIST SP 800-61 Rev. 2 (Incident Response)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

---

### 3.3 Cyber Kill Chain vs ATT&CK: como se complementam

Embora diferentes, ambos os modelos são valiosos e se complementam:

| Aspecto | Cyber Kill Chain | MITRE ATT&CK |
|---------|------------------|--------------|
| **Estrutura** | Linear, sequencial (7 etapas) | Matricial (14 táticas, centenas de técnicas) |
| **Foco** | Narrativa de ataque end-to-end | Catálogo granular de comportamentos |
| **Melhor para** | Comunicar com gestores, visualizar fluxo geral | Engenharia de detecção, threat hunting, análise técnica |
| **Cobertura de movimento lateral** | Implícita em "Ações" | Explícita (tática própria) |
| **Cobertura de identidade/cloud** | Limitada | Forte (matrizes específicas: Enterprise, Cloud, Mobile) |
| **Granularidade** | Baixa (etapas macro) | Alta (sub-técnicas, exemplos, mitigações, detecções) |

**Mapeamento aproximado Kill Chain → ATT&CK:**

| Etapa Kill Chain | Táticas ATT&CK correspondentes | Observações |
|------------------|--------------------------------|-------------|
| 1. Reconhecimento | Reconnaissance | Coleta passiva/ativa externa |
| 2. Arsenalização | Resource Development | Ocorre fora da rede; raramente observável diretamente no host |
| 3. Entrega | Initial Access | Phishing, Drive-by, Exploit Public-Facing Application, Supply Chain |
| 4. Exploração | Execution, Privilege Escalation | Execução de código; pode incluir escalada imediata |
| 5. Instalação | Persistence, Defense Evasion | Persistência + técnicas para esconder (ex: obfuscar, desabilitar logs) |
| 6. C2 | Command and Control | Comunicação bidirecional com infraestrutura do atacante |
| 7. Ações sobre o objetivo | Discovery, Lateral Movement, Credential Access, Collection, Exfiltration, Impact | Kill Chain agrupa tudo aqui; ATT&CK separa em 6+ táticas distintas |

**Limitações do mapeamento:**
- **Não é 1-para-1:** um ataque real pode revisitar táticas (ex: ganhar persistência inicial, fazer discovery, movimento lateral, ganhar nova persistência em outro host).
- **Arsenalização é invisível na telemetria do host:** você não verá logs do seu EDR sobre o atacante compilando malware no laptop dele. Aqui, Threat Intelligence externa (ex: feeds de IOCs, análise de malware samples) é mais útil.
- **Movimento Lateral não tem etapa própria na Kill Chain clássica**, mas é crítico em ambientes corporativos.

**Quando usar cada modelo:**

- **Kill Chain:** apresentações executivas, workshops com áreas de negócio, planejamento macro de controles ("precisamos melhorar detecção de C2").
- **ATT&CK:** engenharia de detecção, criação de regras SIEM/EDR, threat hunting, exercícios de Red/Purple Team, análise de incidentes técnicos.

**Exemplo prático híbrido:**

Um ataque de ransomware pode seguir esta narrativa:
1. **(Kill Chain: Entrega / ATT&CK: Initial Access - T1566.001 Spearphishing Attachment):** e-mail malicioso com Excel.
2. **(Kill Chain: Exploração / ATT&CK: Execution - T1204.002 User Execution: Malicious File):** usuário habilita macros.
3. **(Kill Chain: Instalação / ATT&CK: Persistence - T1547.001 Registry Run Keys):** macro cria chave HKCU\Run.
4. **(ATT&CK: Defense Evasion - T1562.001 Impair Defenses):** malware desabilita Windows Defender.
5. **(Kill Chain: C2 / ATT&CK: C2 - T1071.001 Application Layer Protocol: Web):** malware faz beaconing HTTPS para domínio DGA.
6. **(ATT&CK: Credential Access - T1003.001 LSASS Memory):** dump de credenciais via Mimikatz.
7. **(ATT&CK: Lateral Movement - T1021.002 SMB/Windows Admin Shares):** espalha para outros hosts via PsExec.
8. **(Kill Chain: Ações / ATT&CK: Impact - T1486 Data Encrypted for Impact):** criptografa arquivos e exibe nota de resgate.

Observe como o ATT&CK dá granularidade (sub-técnicas, IDs únicos) enquanto a Kill Chain oferece a narrativa linear.

**Referências adicionais:**
- [Lockheed Martin: Cyber Kill Chain](https://www.lockheedmartin.com/en-us/capabilities/cyber/cyber-kill-chain.html)
- [CrowdStrike: Cyber Kill Chain explicada](https://www.crowdstrike.com/en-us/cybersecurity-101/cyberattacks/cyber-kill-chain/)
- [Pyramid of Pain (David J. Bianco)](https://detect-respond.blogspot.com/2013/03/the-pyramid-of-pain.html)

---

### 3.4 Pyramid of Pain: priorizando detecções eficazes

A **Pyramid of Pain** (Pirâmide da Dor), criada por David J. Bianco, ajuda a priorizar quais indicadores de comprometimento (IOCs — Indicators of Compromise) e comportamentos (IOAs — Indicators of Activity) causam mais "dor" ao atacante quando detectados e bloqueados.

```
                    /\
                   /  \  TTPs (Tactics, Techniques, Procedures)
                  /____\
                 /      \  Tools
                /________\
               /          \  Network/Host Artifacts
              /____________\
             /              \  Domain Names
            /________________\
           /                  \  IP Addresses
          /____________________\
         /                      \  Hash Values
        /________________________\
```

**Níveis da pirâmide (de baixo para cima, do menos para o mais eficaz):**

1. **Hash Values (valores hash de arquivos):** fácil de trocar — atacante muda 1 byte, hash muda completamente. Bloqueio causa pouca dor.
2. **IP Addresses:** fácil trocar de servidor ou usar proxies/CDN. Dor baixa.
3. **Domain Names:** mais custoso trocar (registro, infraestrutura), mas ainda relativamente fácil. Dor moderada.
4. **Network/Host Artifacts:** padrões de tráfego (ex: User-Agent customizado, estrutura de payload C2), mutexes, nomes de arquivos específicos. Exige reengenharia parcial; dor moderada-alta.
5. **Tools:** ferramentas específicas (ex: Cobalt Strike, Mimikatz). Trocar de ferramenta exige treinar operadores, ajustar playbooks. Dor alta.
6. **TTPs (Tactics, Techniques, Procedures):** a forma como o atacante opera — sequência de comandos, técnicas ATT&CK favoritas, comportamento operacional. Mudar TTPs exige redesenhar o ataque inteiro. **Dor máxima.**

**Aplicação prática:**

- **Não se baseie apenas em hashes e IPs:** esses IOCs são frágeis e exigem atualização constante.
- **Invista em detecções comportamentais e baseadas em TTPs:** detectar "PowerShell baixando e executando código da internet" (comportamento) é mais robusto que bloquear um hash específico.
- **Use IOCs de curto prazo para resposta imediata, IOAs de longo prazo para resiliência.**

Exemplo: em vez de criar regra "bloquear conexão para IP X.X.X.X", crie "alertar quando processo Office iniciar PowerShell com flags de download (`-EncodedCommand`, `DownloadString`)". Assim, mesmo que o atacante mude IP ou domínio, o comportamento suspeito ainda será detectado.

**Referência:**
- [Pyramid of Pain - David J. Bianco](https://detect-respond.blogspot.com/2013/03/the-pyramid-of-pain.html)

---

## 4. Defesa em Camadas (Defense-in-Depth)

O conceito de **defesa em camadas** (defense-in-depth) parte do princípio de que não existe um único controle capaz de proteger totalmente um ambiente. Em vez disso, utiliza-se uma combinação de controles em diferentes níveis para compensar falhas e aumentar a resiliência.

As principais camadas de defesa são:

### 4.1 Física
Proteção do acesso físico a ambientes e equipamentos (ex: controle de acesso biométrico, câmeras de segurança, salas seguras com ar-condicionado redundante).

**Por que importa:** um atacante com acesso físico pode roubar discos, instalar keyloggers de hardware, ou fazer boot de pendrives maliciosos. Datacenters e armários de rede devem ser trancados e auditados.

### 4.2 Rede
Segmentação e filtragem do tráfego (ex: VLANs — redes virtuais separadas, firewalls, IDS/IPS — sistemas que detectam e bloqueiam ataques na rede, VPNs — túneis criptografados).

**Sinais/telemetrias relevantes (ambiente híbrido):**
- **DNS:** queries para domínios DGA (gerados por algoritmo, típicos de malware), DNS-TXT incomum (usado para exfiltração ou C2).
- **Proxy/Firewall:** conexões para IPs de má reputação, User-Agent anômalo, tráfego para portas incomuns.
- **NDR (Network Detection and Response):** padrões de beaconing (conexões regulares em intervalos fixos, típicas de C2), conexões SMB/RDP externas, tráfego criptografado suspeito.

Exemplo: firewall detecta tráfego HTTPS para domínio registrado há 2 dias com certificado autoassinado — forte indicador de C2.

### 4.3 Endpoint
Proteção de dispositivos finais — desktops, laptops, servidores (ex: antivírus, EDR — Endpoint Detection and Response, hardening — desabilitar serviços desnecessários, controle de aplicações — permitir apenas softwares aprovados, patches atualizados).

**Sinais/telemetrias relevantes (Windows/Linux):**
- **Sysmon (Windows):** Event ID 1 (criação de processo), Event ID 7 (carregamento de DLL), Event ID 10 (acesso a processo — ex: dump de LSASS), Event ID 3 (conexão de rede).
- **EDR:** execução de LOLBins suspeitos (PowerShell, certutil, regsvr32 com flags anômalos), injeção de código, hooks em APIs sensíveis.
- **PowerShell ScriptBlock Logging (Event ID 4104):** scripts codificados em Base64, download de payloads, execução remota.

Exemplo: Sysmon detecta `powershell.exe` filho de `WINWORD.EXE` executando `-EncodedCommand` — claro sinal de macro maliciosa.

### 4.4 Identidade
Gestão rigorosa de acessos (ex: IAM — Identity and Access Management, gestão de quem pode acessar o quê; MFA — Multi-Factor Authentication, exigir senha + token/SMS; PAM — Privileged Access Management, controle de contas administrativas; SSO — Single Sign-On, login único).

**Sinais/telemetrias relevantes (híbrido):**
- **Azure AD / Entra ID:** sign-ins de IPs anômalos, Conditional Access bloqueios, consentimento de aplicativos OAuth suspeitos, criação de service principals.
- **Active Directory on-prem:** eventos 4624 (logon sucesso), 4625 (falha), 4768/4769 (Kerberos TGT/TGS), 4672 (privilégios especiais atribuídos).
- **AWS CloudTrail:** chamadas STS AssumeRole incomuns, criação de chaves IAM, mudanças em políticas.

Exemplo: usuário de RH faz login às 3h da manhã de IP na Rússia, nunca visto antes — forte indicador de credential stuffing.

### 4.5 Aplicação
Blindagem da lógica de negócio (ex: WAF — Web Application Firewall, filtra ataques como SQL Injection; desenvolvimento seguro — OWASP Top 10; SAST/DAST — análise estática/dinâmica de código; RASP — Runtime Application Self-Protection, protege app em execução).

**Sinais/telemetrias relevantes:**
- **WAF logs:** tentativas de SQL Injection, XSS (Cross-Site Scripting), Path Traversal.
- **Logs de aplicação:** erros de autenticação em massa, acessos a endpoints administrativos, mudanças de configuração críticas.

Exemplo: WAF bloqueia requisição `GET /admin.php?id=1' OR '1'='1` — tentativa de SQL Injection.

### 4.6 Dados
Proteção e recuperação de informações (ex: classificação — marcar dados sensíveis, criptografia em trânsito/repouso, DLP — Data Loss Prevention, impedir cópia de dados sensíveis, backups imutáveis, disaster recovery).

**Sinais/telemetrias relevantes:**
- **DLP:** usuário tentando enviar arquivos com CPFs por e-mail externo, upload de código-fonte para Dropbox pessoal.
- **Cloud Storage:** acessos incomuns a buckets S3/Azure Blob, mudança de permissões para público, download massivo de arquivos do SharePoint.

Exemplo: DLP alerta — usuário baixou 10.000 registros do banco de clientes e tentou enviá-los para Gmail.

### 4.7 Observabilidade
Capacidade de monitorar e reagir rapidamente (ex: SIEM — Security Information and Event Management, correlaciona logs; SOAR — Security Orchestration, Automation and Response, automatiza resposta; UEBA — User and Entity Behavior Analytics, detecta anomalias; NDR — Network Detection and Response).

**Por que importa:** sem visibilidade, você está "cego" — o atacante pode estar na rede há meses e você não sabe. Logs centralizados, dashboards de KPIs de segurança (tempo médio de detecção/resposta), e alertas bem calibrados são essenciais.

Evite o erro comum de confiar em um único controle ("tem antivírus, logo está seguro"). O ideal é combinar camadas para cobrir lacunas e reduzir riscos. Em ambientes híbridos, isso significa integrar visibilidade on-prem (Sysmon, AD logs) com cloud (Azure AD, AWS CloudTrail, Microsoft Defender).

**Referências:**
- [CIS Controls v8](https://www.cisecurity.org/controls/cis-controls-list)
- [SANS Top 20 Critical Security Controls](https://www.sans.org/blog/cis-controls-v8/)

---

## 5. Boas práticas de Engenharia de Detecção

**Engenharia de Detecção** é a disciplina de criar, testar e manter regras, alertas e casos de uso que identificam comportamentos maliciosos. Não basta comprar ferramentas — é preciso alimentá-las com conteúdo de qualidade.

### 5.1 Detections-as-Code

Trate suas detecções como código:
- **Versionamento:** use Git para controlar versões de regras SIEM, queries KQL, regras Sigma.
- **Peer review:** outro analista revisa a regra antes de ir para produção.
- **CI/CD:** pipeline automatizado testa a regra contra logs históricos (datasets de teste) antes de deploy.

Ferramentas: Sigma (formato agnóstico de regra), Elastic Detection Rules (regras open-source da Elastic), Microsoft Sentinel Content Hub.

### 5.2 Testes de Detecção

Cada detecção deve ser testada:
- **True Positives (TP):** a regra detecta o ataque real? Rode simulações (ex: Atomic Red Team, Caldera) para validar.
- **False Positives (FP):** a regra gera alertas falsos? Teste com atividade legítima comum no seu ambiente.
- **False Negatives (FN):** a regra deixa passar alguma variante do ataque? Teste com técnicas semelhantes.

Exemplo: regra detecta `mimikatz.exe` — mas e se o atacante renomear para `update.exe`? Melhor detectar comportamento (acesso ao processo `lsass.exe` por processo não assinado).

### 5.3 Cobertura ATT&CK e Navigator

Use o **ATT&CK Navigator** para visualizar quais técnicas você cobre:
1. Liste todas as suas detecções e mapeie cada uma para técnicas ATT&CK.
2. Importe no Navigator.
3. Identifique lacunas: técnicas sem detecção, especialmente as mais usadas no seu setor (ex: Threat Intelligence reports).

Priorize cobrir **táticas críticas** primeiro: Initial Access, Persistence, Credential Access, Lateral Movement, Exfiltration.

### 5.4 Hipóteses de Threat Hunting

**Threat Hunting** é busca proativa de ameaças, sem esperar alertas. Formule hipóteses baseadas em:
- **Intel de ameaças:** grupo X usa técnica Y, vamos caçar sinais disso aqui.
- **Lacunas de detecção:** não temos regra para T1003.001 (LSASS dump), vamos buscar manualmente.
- **Anomalias:** processo estranho `svchost.exe` rodando de pasta incomum.

Documente hipóteses, queries usadas, e resultados — alimenta melhoria contínua.

**Referências:**
- [Sigma HQ (regras open-source)](https://github.com/SigmaHQ/sigma)
- [Atomic Red Team (testes de técnicas ATT&CK)](https://github.com/redcanaryco/atomic-red-team)
- [MITRE ATT&CK Navigator](https://mitre-attack.github.io/attack-navigator/)

---

## 6. Mini-caso prático: Ataque híbrido com queries de detecção

Vamos simular um ataque real em ambiente híbrido (Windows on-prem + Azure) e construir queries de detecção para cada etapa.

### Cenário: Campanha de ransomware via phishing

**Narrativa do ataque:**
1. **Initial Access:** funcionário do financeiro recebe e-mail de "fatura atrasada" com anexo `Fatura_2025.xlsm` (Excel com macro).
2. **Execution:** usuário habilita macros; VBA executa PowerShell codificado que baixa segunda etapa.
3. **Persistence:** payload cria chave de registro `HKCU\Software\Microsoft\Windows\CurrentVersion\Run` apontando para script em `%APPDATA%`.
4. **Defense Evasion:** desabilita Windows Defender via PowerShell (`Set-MpPreference -DisableRealtimeMonitoring $true`).
5. **C2:** malware faz beaconing HTTPS para domínio DGA (`xj92kf1a.top`) a cada 15 minutos.
6. **Credential Access:** executa Mimikatz para dump de LSASS, obtém hash NTLM de conta de administrador de domínio.
7. **Lateral Movement:** usa Pass-the-Hash via PsExec para espalhar para servidor de arquivos.
8. **Collection:** enumera compartilhamentos de rede e copia arquivos críticos.
9. **Exfiltration:** envia dados para bucket S3 público (abusando de credencial AWS roubada de script).
10. **Impact:** criptografa arquivos e exibe nota de resgate.

---

### Queries de detecção (KQL e Sigma)

#### 1. Detecção: Office iniciando PowerShell com comando codificado (Execution)

**Plataforma:** Microsoft Defender for Endpoint / Microsoft Sentinel

**KQL (Kusto Query Language):**
```kusto
DeviceProcessEvents
| where Timestamp > ago(30d)
| where InitiatingProcessFileName in~ ("WINWORD.EXE", "EXCEL.EXE", "POWERPNT.EXE")
| where FileName in~ ("powershell.exe", "powershell_ise.exe")
| where ProcessCommandLine has_any ("-enc", "-EncodedCommand", "FromBase64String", "DownloadString", "IEX")
| project Timestamp, DeviceName, AccountName, InitiatingProcessFileName, ProcessCommandLine
```

**O que detectar:** documento Office abrindo PowerShell com sinais de código codificado/download — típico de macros maliciosas.

---

#### 2. Detecção: Alteração de persistência via chave de registro (Persistence)

**Plataforma:** Microsoft Defender / Sysmon

**KQL:**
```kusto
DeviceRegistryEvents
| where Timestamp > ago(7d)
| where RegistryKey has @"Software\Microsoft\Windows\CurrentVersion\Run"
| where RegistryValueName != "" and RegistryValueData has_any ("powershell", "cmd", "wscript", "cscript", ".vbs", ".js")
| project Timestamp, DeviceName, AccountName, RegistryKey, RegistryValueName, RegistryValueData, InitiatingProcessFileName
```

**O que detectar:** criação/modificação de chaves de inicialização automática apontando para scripts — forte indicador de persistência.

---

#### 3. Detecção: Desabilitação do Windows Defender (Defense Evasion)

**Plataforma:** Microsoft Defender / Sysmon

**KQL:**
```kusto
DeviceProcessEvents
| where Timestamp > ago(7d)
| where FileName in~ ("powershell.exe", "powershell_ise.exe")
| where ProcessCommandLine has_any ("Set-MpPreference", "DisableRealtimeMonitoring", "DisableBehaviorMonitoring", "DisableIOAVProtection")
| project Timestamp, DeviceName, AccountName, ProcessCommandLine, InitiatingProcessFileName
```

**O que detectar:** comandos PowerShell que desativam proteções do Defender — técnica comum de malware.

---

#### 4. Detecção: DNS queries para domínios DGA (C2)

**Plataforma:** Azure Firewall / DNS logs / Proxy

**KQL (Azure Firewall DNS Proxy Logs):**
```kusto
AzureDiagnostics
| where Category == "AzureFirewallDnsProxy"
| where TimeGenerated > ago(24h)
| extend QueryName = tolower(tostring(parse_json(msg_s).query))
| where QueryName matches regex @"^[a-z0-9]{8,12}\.(top|xyz|info|club)$"
| summarize Count=count() by QueryName, SourceIp = ClientIp
| where Count > 5
| order by Count desc
```

**O que detectar:** domínios com nomes aleatórios curtos e TLDs suspeitos (.top, .xyz) consultados repetidamente — padrão DGA típico de malware.

---

#### 5. Detecção: Acesso ao processo LSASS (Credential Access)

**Plataforma:** Sysmon (Event ID 10)

**Sigma:**
```yaml
title: Suspicious Access to LSASS Process
description: Detecta acessos ao processo lsass.exe por processos não assinados ou incomuns
logsource:
  product: windows
  service: sysmon
detection:
  selection:
    EventID: 10
    TargetImage|endswith: '\lsass.exe'
    GrantedAccess:
      - '0x1010'
      - '0x1410'
      - '0x1438'
  filter:
    SourceImage|endswith:
      - '\wmiprvse.exe'
      - '\taskmgr.exe'
      - '\procexp64.exe'
  condition: selection and not filter
level: high
falsepositives:
  - Ferramentas administrativas legítimas (Process Explorer, alguns antivírus)
tags:
  - attack.credential_access
  - attack.t1003.001
```

**O que detectar:** processos acessando memória do LSASS (onde ficam credenciais) — Mimikatz, Cobalt Strike, outros dumpers.

---

#### 6. Detecção: Uso de LOLBin regsvr32 para download remoto (Defense Evasion / Execution)

**Plataforma:** Microsoft Defender / Sysmon

**KQL:**
```kusto
DeviceProcessEvents
| where Timestamp > ago(7d)
| where FileName =~ "regsvr32.exe"
| where ProcessCommandLine has_any ("http://", "https://", ".sct", "scrobj.dll")
| project Timestamp, DeviceName, AccountName, ProcessCommandLine, InitiatingProcessFileName
```

**O que detectar:** regsvr32 sendo abusado para executar scripts remotos (técnica bypass de AppLocker/UAC).

---

#### 7. Detecção: Movimento lateral via PsExec (Lateral Movement)

**Plataforma:** Microsoft Defender / Sysmon

**KQL:**
```kusto
DeviceProcessEvents
| where Timestamp > ago(7d)
| where FileName =~ "psexec.exe" or ProcessCommandLine has "psexec"
     or (FileName =~ "psexesvc.exe")
| project Timestamp, DeviceName, AccountName, ProcessCommandLine, InitiatingProcessAccountName
```

**O que detectar:** uso de PsExec (ferramenta Sysinternals comum para administração, mas muito abusada por atacantes para movimento lateral).

---

#### 8. Detecção: Consentimento de aplicativo OAuth suspeito (Cloud - Azure)

**Plataforma:** Microsoft Sentinel / Azure AD logs

**KQL:**
```kusto
AuditLogs
| where TimeGenerated > ago(7d)
| where OperationName has_any ("Consent to application", "Add service principal")
| where Result =~ "success"
| extend AppDisplayName = tostring(parse_json(tostring(TargetResources[0])).displayName)
| extend UserPrincipalName = tostring(InitiatedBy.user.userPrincipalName)
| project TimeGenerated, UserPrincipalName, AppDisplayName, OperationName, CorrelationId
```

**O que detectar:** usuários concedendo permissões a apps OAuth desconhecidos — técnica de phishing moderno para roubar acesso a Office 365/Azure sem senha.

---

#### 9. Detecção: Acesso anômalo a bucket S3 (Exfiltration - AWS)

**Plataforma:** AWS CloudTrail / Splunk / Sentinel

**KQL (assumindo CloudTrail ingerido no Sentinel):**
```kusto
AWSCloudTrail
| where TimeGenerated > ago(24h)
| where EventName in~ ("GetObject", "ListObjects")
| where isnotempty(SourceIpAddress)
| summarize Count=count(), DataTransferred=sum(ResponseElements.bytesTransferredOut) by SourceIpAddress, UserIdentityArn, BucketName=tostring(RequestParameters.bucketName)
| where Count > 100 or DataTransferred > 10000000 // mais de 10MB ou 100 acessos
| order by DataTransferred desc
```

**O que detectar:** downloads massivos de S3 de IP externo ou usuário incomum — possível exfiltração de dados.

---

### Observações sobre as queries

- **KQL** é a linguagem nativa de Microsoft Sentinel, Defender, Azure Data Explorer.
- **Sigma** é formato agnóstico — pode ser convertido para KQL, Splunk, Elastic, QRadar, etc.
- Queries reais precisam de **tuning** no seu ambiente: ajustar thresholds, adicionar exceções (ex: admins legítimos usando PsExec), correlacionar múltiplos eventos.
- **Teste antes de produção:** rode queries em modo de auditoria por dias/semanas, analise falsos positivos, ajuste.

**Ferramentas úteis:**
- [Uncoder.io](https://uncoder.io/) — converte Sigma para KQL, Splunk, Elastic, etc.
- [Atomic Red Team](https://github.com/redcanaryco/atomic-red-team) — teste técnicas ATT&CK para validar detecções.
- [Microsoft Sentinel Content Hub](https://learn.microsoft.com/en-us/azure/sentinel/sentinel-content-hub) — regras prontas.

---

## 7. Glossário básico

Termos técnicos citados nesta aula, em ordem alfabética:

- **ATP (Advanced Threat Protection):** soluções que detectam ameaças avançadas e persistentes.
- **Beaconing:** padrão de comunicação regular entre malware e servidor C2 (ex: a cada 10 min).
- **C2 (Command and Control):** infraestrutura do atacante para controlar sistemas comprometidos.
- **CloudTrail:** serviço da AWS que registra chamadas de API (quem fez o quê, quando).
- **Credential Stuffing:** ataque automatizado usando pares usuário/senha vazados de outros sites.
- **DGA (Domain Generation Algorithm):** algoritmo que cria domínios aleatórios para C2, dificultando bloqueio.
- **DLP (Data Loss Prevention):** ferramentas que impedem vazamento de dados sensíveis (ex: bloquear envio de CPFs por e-mail).
- **Drive-by Download:** infecção apenas visitando site comprometido, sem clicar em nada.
- **EDR (Endpoint Detection and Response):** ferramenta avançada de proteção de endpoints, com visibilidade profunda e caça a ameaças (ex: Microsoft Defender for Endpoint, CrowdStrike Falcon).
- **IAM (Identity and Access Management):** gestão de identidades e acessos (quem pode acessar o quê).
- **IDS/IPS (Intrusion Detection/Prevention System):** sistemas que detectam (IDS) ou bloqueiam (IPS) ataques na rede.
- **IOA (Indicator of Activity):** indicadores de atividade/comportamento malicioso (ex: PowerShell baixando executável).
- **IOC (Indicator of Compromise):** indicadores de comprometimento (ex: hash de malware, IP de C2, domínio malicioso).
- **KQL (Kusto Query Language):** linguagem de consulta usada no Microsoft Sentinel, Defender, Azure Data Explorer.
- **LOLBins (Living Off the Land Binaries):** ferramentas legítimas do sistema operacional abusadas por atacantes (ex: PowerShell, certutil, regsvr32).
- **MFA (Multi-Factor Authentication):** autenticação em múltiplos fatores (ex: senha + código SMS).
- **NDR (Network Detection and Response):** solução que monitora tráfego de rede para detectar ameaças.
- **OAuth:** protocolo de autorização usado por apps para acessar recursos (ex: "logar com Google").
- **PAM (Privileged Access Management):** gestão de contas privilegiadas (administradores).
- **Pass-the-Hash:** técnica de roubar hash de senha (sem a senha em texto claro) e usá-lo para autenticar.
- **RASP (Runtime Application Self-Protection):** proteção que roda dentro da aplicação, detectando ataques em tempo real.
- **SAST/DAST (Static/Dynamic Application Security Testing):** análise de segurança de código (estática) ou aplicação rodando (dinâmica).
- **SIEM (Security Information and Event Management):** plataforma que coleta, correlaciona logs e gera alertas (ex: Splunk, Microsoft Sentinel, QRadar).
- **Sigma:** formato open-source e agnóstico de regras de detecção, conversível para várias plataformas.
- **SOAR (Security Orchestration, Automation and Response):** automação de resposta a incidentes (ex: isolar máquina infectada automaticamente).
- **SSO (Single Sign-On):** login único para acessar múltiplos sistemas.
- **STS (Security Token Service):** serviço AWS que gera tokens temporários para assumir roles IAM.
- **Sysmon (System Monitor):** ferramenta Microsoft gratuita que gera logs detalhados de processos, rede, registry no Windows.
- **TTP (Tactics, Techniques, Procedures):** comportamento operacional do atacante (como ele age).
- **UEBA (User and Entity Behavior Analytics):** análise comportamental de usuários/entidades para detectar anomalias (ex: usuário de RH acessando banco de dados financeiro).
- **WAF (Web Application Firewall):** firewall específico para proteger aplicações web (bloqueia SQL Injection, XSS, etc.).

---

**Referências finais:**
- [MITRE ATT&CK](https://attack.mitre.org/)
- [MITRE D3FEND](https://d3fend.mitre.org/)
- [NIST Cybersecurity Framework](https://www.nist.gov/cyberframework)
- [CIS Controls v8](https://www.cisecurity.org/controls/)
- [SANS Reading Room (papers técnicos)](https://www.sans.org/white-papers/)
- [Microsoft Cybersecurity Reference Architectures](https://learn.microsoft.com/en-us/security/cybersecurity-reference-architecture/mcra)

---

