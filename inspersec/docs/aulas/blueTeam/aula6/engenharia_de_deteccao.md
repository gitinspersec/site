# Engenharia de Detecção

A **Engenharia de Detecção** é o campo da segurança cibernética voltado ao **desenvolvimento sistemático de conteúdo de detecção** — regras, alertas, correlações e lógica — que permitem identificar comportamentos maliciosos ou anômalos em tempo hábil.  
Diferente da simples configuração de alertas, a engenharia de detecção se apoia em **métodos científicos e automação**, buscando **precisão, cobertura e rastreabilidade** no ciclo de vida das detecções.

Partimos do princípio de que o leitor já domina conceitos como **SIEM/SOAR**, **logs estruturados**, **MITRE ATT&CK**, **KQL/SPL** e **detections-as-code**.

---

## Ciclo de Vida do Conteúdo de Detecção

A engenharia de detecção segue um ciclo iterativo, baseado em hipótese, design, validação e medição.

### 1. Requisito / Hipótese
Toda detecção nasce de uma **pergunta de segurança**.

> Exemplo: “Como perceberíamos um *credential dumping* no ambiente?”

A hipótese define o **comportamento esperado do atacante** e orienta as demais etapas.

---

### 2. Mapeamento de Dados
Com base na hipótese, identificam-se as **fontes de log** e os **campos necessários**.

| Fonte | Tipo de Log | Campos Relevantes |
|--------|--------------|------------------|
| Sysmon | Event ID 10 (Process Access) | `ProcessName`, `TargetImage` |
| Windows Security | 4624 / 4625 | `LogonType`, `AuthenticationPackageName` |
| EDR | Process events | `cmdline`, `parent`, `user` |

> Exemplo: logs que contenham chamadas a `lsass.exe` ou `mimikatz.exe` podem ser úteis para detectar *credential dumping*.

---

### 3. Design da Regra
Aqui se traduz a hipótese em **lógica detectável**, definindo:
- **Condições:** o que deve acionar o alerta.  
- **Janela temporal:** intervalo para agregações (ex: 5 min, 1h).  
- **Exceções:** exclusões conhecidas, como ferramentas legítimas de administração.  

> Exemplo em pseudocódigo:
```sql
event_source = "sysmon"
where TargetImage == "lsass.exe"
and ProcessName in ("procdump.exe", "mimikatz.exe")
| window 5m
| alert if count > 0
```

### 4. Validação / QoS

O conteúdo é testado quanto à eficácia e precisão:

- Uso de datasets reais e test harnesses;

- Execução de simulações de ataque (BAS) para validar cobertura;

- Medição de falsos positivos e performance.

>Ferramentas como Atomic Red Team ou Prelude podem ser usadas para validação controlada.

### 5. Deploy Versionado

A detecção é tratada como código (detections-as-code).
O processo envolve:

- Controle de versão (Git);

- Revisão por pares via Pull Request;

- Controle de ativação via feature flags.

> Exemplo: cada detecção possui seu próprio arquivo YAML com metadados, lógica, owner e tags.


### 6. Tuning e Manutenção

Após o deploy, as regras devem ser monitoradas e ajustadas continuamente:

- Redução de falsos positivos/negativos;

- Adaptação a mudanças de ambiente e novas ameaças;

- Descontinuação de regras obsoletas (depreciação).

> Exemplo: se uma regra gerar alertas de administradores legítimos, é necessário incluir exceções de contexto.

### 7. Medição e Métricas

A eficácia das detecções é avaliada por indicadores quantitativos:

- Precisão / Recall: equilíbrio entre sensibilidade e ruído;

- Coverage MITRE ATT&CK: mapeamento tático/técnico;

- Impacto em MTTD / MTTR: tempo médio de detecção e resposta.

> Uma boa detecção deve reduzir o Mean Time To Detect sem aumentar falsos positivos.

---

## Resumo de Tags e Metadados

Cada detecção deve conter metadados padronizados para rastreabilidade e automação:

|   Campo   | Descrição	| Exemplo |
| --------- | --------- | ------- |
|id  	    |Identificador único|	SEC-DET-001|
|name	    |Nome da regra|	Credential Dumping Detection|
|owner	    |Responsável pelo conteúdo|	time.blue@empresa.com|
|severity   |	Nível de severidade|	High|
|status	    |Estado atual|	Active / Deprecated|
|tags	    |Classificação|	ATT&CK:T1003, Windows, LSASS|
|description|	Resumo técnico|	“Detecta tentativas de acesso ao processo LSASS.”|
|runbook	|Documento de resposta associado|	runbooks/cred_dump_response.md|

> **Boas práticas:** manter nomenclatura consistente, usar tags MITRE, documentar exceções e sempre vincular o runbook de resposta.

---

## Conclusão

A Engenharia de Detecção é um processo contínuo e interdisciplinar, unindo segurança ofensiva, dados e automação.
Ela transforma conhecimento sobre ameaças em mecanismos mensuráveis de defesa, reduzindo o tempo de resposta e aumentando a maturidade do SOC.

> Em última análise, a detecção não é apenas sobre alertar, mas sobre compreender o comportamento adversário e reagir com inteligência e precisão.

---
