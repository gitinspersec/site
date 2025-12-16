# Gestão de Vulnerabilidades e Exposição

A **Gestão de Vulnerabilidades e Exposição (GVE)** é o processo contínuo de **identificar, priorizar, corrigir e monitorar vulnerabilidades e exposições** em ativos de tecnologia.  
O objetivo é **reduzir a superfície de ataque** e **diminuir o risco** por meio de ações estruturadas e mensuráveis.

> Em essência: **descobrir → avaliar → tratar → verificar → melhorar.**

---

## Conceito

- **Vulnerabilidade:** fraqueza técnica ou de configuração explorável (ex.: CVE em um serviço desatualizado).
- **Exposição:** condição que torna o ativo acessível ao ataque (ex.: serviço sensível exposto na internet).
- **Gestão:** ciclo de processos, ferramentas e métricas para **transformar descobertas em ações corretivas**.

---

## Importância

- **Reduzir risco real:** fechar portas exploráveis por ransomware, APTs e crimeware.  
- **Priorizar com contexto:** focar no que tem maior probabilidade/impacto de exploração (tempo é limitado).  
- **Atender compliance:** requisitos de PCI-DSS, ISO 27001, SOC 2, LGPD e reguladores.  
- **Apoiar resposta a incidentes:** diminuir a janela de oportunidade para adversários.  
- **Melhorar resiliência:** manter baseline seguro e acelerar remediações futuras.

---

## Ciclo de Gestão (com exemplos)

1. **Descoberta e Inventário**  
   - Mapear ativos on-prem, cloud e shadow IT.  
   - Ex.: varreduras autenticadas, CMDB, ASM (External Attack Surface Management).

2. **Avaliação e Classificação**  
   - Rodar scanners (autenticados/de rede), SCA, SAST/DAST para apps.  
   - Ex.: CVSS, EPSS, KEV (Known Exploited Vulnerabilities) para priorizar.

3. **Priorização e Planejamento**  
   - Combinar severidade técnica + exploitabilidade + exposição + criticidade do ativo.  
   - Ex.: dar prioridade máxima a CVEs listadas em KEV em sistemas expostos à internet.

4. **Correção e Mitigação**  
   - Aplicar patches, reconfigurar, isolar, bloquear IOC/IOA em WAF/EDR/SIEM.  
   - Ex.: virtual patch em WAF enquanto a atualização é agendada.

5. **Validação e Verificação**  
   - Re-scan, testes de regressão, validação de mudança em produção.  
   - Ex.: pipeline de CI/CD com gates de segurança para evitar reintrodução.

6. **Monitoramento Contínuo e Métricas**  
   - Dashboards de MTTR/MTTM, coverage de patches, SLA por criticidade.  
   - Ex.: alertas automáticos para novas CVEs que afetam softwares inventariados.

> **Dica:** alinhe o ciclo com janelas de mudança e esteiras DevSecOps para minimizar impacto operacional.

---

## Cautelas e Boas Práticas

- **Contextualize além do CVSS:** use **EPSS, KEV, exploração ativa** e criticidade do ativo.  
- **Evite backlog infinito:** defina **SLA por criticidade** e revise periodicamente.  
- **Automatize onde possível:** integrações com ITSM, CMDB, CI/CD e MDM/EDR.  
- **Segurança em camadas:** combine patching, hardening, segmentação e controles compensatórios.  
- **Comunicação clara:** envolver times de produto, infraestrutura e negócio para viabilizar mudanças.  
- **Auditoria e rastreabilidade:** registre exceções justificadas e revise-as com prazo definido.

---

## Conclusão

A **Gestão de Vulnerabilidades e Exposição** é essencial para **reduzir superfície de ataque e risco real**.  
Quando orientada por contexto (exploitabilidade + negócio) e apoiada por automação, ela viabiliza **respostas rápidas e priorização eficaz**.

> “Patching sem priorização é esforço; priorização sem ação é risco.”