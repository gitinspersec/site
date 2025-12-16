# Inteligência de Segurança

A **Inteligência de Segurança** é a prática de **coletar, padronizar e analisar dados** gerados por redes, aplicações e outras infraestruturas de TI ou físicas, com o objetivo de gerar **conhecimento** para a **tomada de decisões estratégicas** relacionadas à segurança da organização.

### 1.1 Introdução ( Importância )

A Inteligência de Segurança é essencial na proteção de informações, softwares e objetos físicos. Na área da cibernética, inteligência de Segurança pode atuar em:

Exemplos de atuação
|Onde|Como|
|----|----|
|Prevenção de ataques cibernéticos|Indentificar IoCs como endereços de IP suspeitos ou padrôes de comportamente anormais, antes que um ataque ocorra|
|Resposta rápida e eficaz a incidentes|Detectar e mitigar ameaças mais rapidamente, reduzindo o impacto de invasões, vazamentos e malware|
|Análise preditiva|Utilizando técnicas de machine learning e analise de dados para prever formas de ataques e fortalecer defesas antes de exploradas|
|Aprimoramento Contínuo|Permite o aperfeiçoamento organizacional, com a melhora de processos, políticas e ferramentas de segurança|

### 1.2 Áreas de Atuação

Algumas das áreas que mais usam Inteligência de Segurança são:

| Tipo | Foco Principal | Exenplos de Organizações | Exemplos de Aplicação |
|------|----------------|------------------------|-----------|
| **Nacional** | Proteção da soberania e dos interesses estratégicos do país | CIA; ABIN; | Monitorar redes, fronteiras, prevenir espionagem e sabotagem |
| **Cibernética** | Defesa contra ameaças virtuais e ataques digitais | CISC; FIRST; CERT/CC; NIST  | Identificar novos malwares, ataques e padrões de segurança |
| **Corporativa** | Proteção de dados, reputação e ativos empresariais | AWS; CloudFlare; Nubank; | Evitar espionagem industrial e vazamento de informações |
| **Operacional** | Apoio a ações em andamento e resposta a incidentes | ComDCiber; INTERPOL; ENISA; | Monitorar SOCs, coordenar respostas e ajustar políticas em tempo real |

---

## Ciclo de Inteligência (CI)

### 2.1 Introdução (O que é?)

O ciclo de inteligência é uma série de sete etapas usadas para converter dados brutos em conhecimento útil e acionável (que pode ser usada imediatamente), esse conhceimente é posteriormente usado em tomadas de decisões conceituadas.

### 2.2 Estrutura

| Etapa | Objetivo |
| ----- | -------- |
| **1º - Planejamento e Direcionamento** | Define o que precisa ser protegido e estabelece a direção para a coleta, com base nas necessidades e prioridades da organização. |
| **2º - Coleta de Dados** | Reúne dados brutos de diversas fontes disponíveis para atender às necessidades definidas na fase anterior. |
| **3º - Processamento** | Organizar, filtrar e normalizar dados para que possam ser analisados. |
| **4º - Análise e Produção** | Avalia, interpreta e sintetiza os dados processados para criar um relatório de inteligência final, que pode incluir probabilidade e certeza. |
| **5º - Disseminação** | Compartilhar resultados com as equipes certas, no formato e tempo adequados. |
| **6º - Avaliação/Consumo** | Os tomadores de decisão utilizam a inteligência na formulação de poíticas e ações |
| **7º - Retroalimentação** | Os receptores da inteligência (tomadores de decisão, lideres, etc) fornecem aprimoramentos, novos requisitos, entre outras informações para iniciar um novo ciclo, fechando o circuito |

### 2.3 Estrutura para Segurança em Cibernética

| Etapa | Objetivo | Exemplos Práticos |
|--------|-----------|-------------------|
| **1º - Planejamento e Direcionamento** | Definir quais ameaças investigar e por quê. Identificar lacunas de segurança e objetivos estratégicos. | Exemplo: "Mapear grupos de ransomware que visam o setor financeiro no Brasil." |
| **2º - Coleta de Dados** | Capturar indicadores de ameaça (IoCs) e dados relevantes de fontes abertas e fechadas. | Logs de rede, honeypots, feeds de IOC, dark web, OSINT, ISACs. |
| **3º - Processamento** | Organizar, filtrar e normalizar dados para que possam ser analisados. | Converter logs, extrair hashes, normalizar dados de DNS, filtrar falsos positivos. |
| **4º - Análise e Produção** | Correlacionar dados e produzir inteligência acionável — identificar atores, táticas (TTPs), motivações e tendências. | Relatório aponta que o grupo FIN7 explora vulnerabilidades em campanhas de phishing. |
| **5º - Disseminação** | Compartilhar resultados com as equipes certas, no formato e tempo adequados. | Alertas para o SOC, relatórios executivos, feeds STIX/TAXII automatizados. |
| **6º & 7º - Avaliação/Consumo e Retroalimentação** | Ajustar o ciclo com base na eficácia das ações e novas ameaças. | O SOC informa que um ataque foi bloqueado, mas surgiram novas variantes. |

---

## Conclusão

A Inteligência de Segurança é mais do que análise de dados — é uma **capacidade organizacional** de **aprender continuamente com o ambiente de ameaças** e **agir com base em evidências**.

> O verdadeiro valor da inteligência está em **transformar informação em decisão** — e decisão em **ação coordenada**.

Nada melhor do que evitar perder o trabalo de 3 anos em 3 minutos.

---

## Referências

- Sumo Logic – *Security Intelligence Glossary*
- FiveCast – *Fundamentos de OSINT: As Cinco Etapas do Ciclo de Inteligência*

---