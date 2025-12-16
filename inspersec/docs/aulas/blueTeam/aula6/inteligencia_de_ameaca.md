# Inteligência de Ameaças (Threat Intelligence)

A **Inteligência de Ameaças (Threat Intelligence, ou TI/CTI)** é o processo sistemático de **coletar, analisar e aplicar informações sobre adversários cibernéticos**, suas motivações, técnicas e infraestrutura.  
O objetivo é **transformar dados brutos em conhecimento acionável**, apoiando decisões estratégicas e táticas de segurança.

Inteligência de Ameaças é incluso na **Inteligência de Segurança,seguindo passos similares**, mas concentrados em ameaças.

---

## Conceito

A inteligência de ameaças vai além de apenas listar indicadores (hashes, IPs, domínios).  
Ela envolve **contexto e análise** — entender *quem*, *como* e *por que* um ataque ocorre.  
Essa inteligência alimenta **SIEMs, EDRs, playbooks de SOAR, hunting e resposta a incidentes**.

> Em essência: **dados → informação → inteligência → ação.**

---

## Importância

- **Antecipar ameaças:** identificar campanhas antes que atinjam a organização.  
- **Aumentar contexto:** enriquecer alertas com reputação, origem e histórico.  
- **Aprimorar priorização:** responder primeiro ao que representa maior risco.  
- **Guiar hunting e engenharia de detecção:** validar hipóteses baseadas em TTPs (Táticas, Técnicas e Procedimentos).  
- **Fortalecer decisões estratégicas:** apoiar planos de defesa, investimentos e políticas corporativas.

> Exemplo prático: se uma campanha de phishing direcionada a bancos latino-americanos está ativa, uma instituição financeira brasileira pode reforçar controles e hunting baseados nas TTPs associadas.

---

## Níveis de Inteligência de Ameaças

A Threat Intelligence é dividida em **quatro níveis complementares**, cada um com objetivos e públicos distintos.

| Nível | Foco | Exemplos | Público-alvo |
|--------|------|-----------|---------------|
| **Estratégico** | Tendências e riscos globais | Relatórios de grupos APT, geopolítica, supply chain | Executivos, CISOs |
| **Operacional** | Campanhas e modos de opeção | “Campanha de ransomware ALPHV visando setor de saúde” | Gestores de segurança, arquitetos |
| **Tático** | Técnicas e comportamentos (TTPs/IOAs) | Abuso de `rundll32.exe`, *living off the land*, persistência via WMI | Engenheiros de detecção, threat hunters |
| **Técnico** | Indicadores observáveis (IOCs) | Hashes, IPs, domínios, URLs maliciosos | Analistas SOC, automações SIEM/SOAR |

> Esses níveis se complementam — do **“por quê” estratégico** ao **“como” técnico** — formando o ciclo completo de inteligência.

---

## Uso Prático da Threat Intelligence

A inteligência de ameaças pode ser aplicada diretamente em diferentes camadas da operação de segurança:

1. **Enriquecimento de Alertas**  
   - Adiciona reputação e contexto a eventos do SIEM/EDR.  
   - Exemplo: classificar um IP como “botnet C2 conhecido” via integração MISP.

2. **Priorização e Risco Contextual**  
   - Atribui maior peso a incidentes relacionados a campanhas **ativas no setor** da empresa.  
   - Exemplo: alertas ligados a ransomware direcionado ao setor financeiro recebem prioridade alta.

3. **Hunting e Validação de Hipóteses**  
   - Caçadores de ameaças usam TTPs e IOAs de relatórios de CTI para **buscar atividade maliciosa não detectada**.  
   - Exemplo: verificar uso de `powershell -enc` em endpoints após alerta sobre grupo APT específico.

---

## Cautelas e Limitações

Nem toda informação de ameaça é útil por muito tempo — é preciso senso crítico e contexto.

- **IOCs têm vida curta:** hashes e IPs mudam rapidamente; use-os como **sinais temporários**, não como verdade permanente.  
- **Foco em comportamentos (IOAs/TTPs):** técnicas e padrões mudam menos e são **mais duradouros**.  
- **Valide fontes:** evite feeds genéricos ou desatualizados. Prefira fontes confiáveis (MISP, OTX, FS-ISAC).  
- **Evite sobrecarga de dados:** excesso de inteligência sem curadoria causa **alert fatigue**.  
- **Privacidade e ética:** cuidado ao compartilhar informações sensíveis de clientes, parceiros ou funcionários.

> “Inteligência sem contexto é apenas ruído.”

---

## Conclusão

A **Inteligência de Ameaças** é o elo entre o conhecimento global e a defesa local.  
Ela permite que organizações **passem de uma postura reativa para proativa**, antecipando riscos e adaptando defesas em tempo real.  
Mais do que dados, Threat Intel é **contexto + propósito + ação**.

> **Gancho:**  
> “Saber que um ataque existe é informação.  
> Saber como ele te afeta é inteligência.”
