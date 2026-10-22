# Aula 4 — Hardening: princípios e métodos

> **Objetivo:** compreender o que é hardening e reconhecer métodos para reduzir a superfície de ataque e manter sistemas em uma configuração segura.

**Duração:** 1h20

## Objetivos de aprendizagem

Ao final da aula, você deverá conseguir:

- Explicar o que é hardening.
- Reconhecer configurações que aumentam a superfície de ataque.
- Descrever métodos comuns de hardening.
- Comparar a configuração de um sistema com uma baseline.
- Justificar uma recomendação e indicar como validar a mudança.

## 1. O que é hardening?

**Hardening** é o processo de configurar sistemas, aplicações e serviços para reduzir riscos. Isso inclui remover o que não é necessário, limitar acessos, aplicar configurações seguras e manter o ambiente atualizado.

Um sistema pode ter serviços, contas, permissões ou opções que não são necessários para sua função. Cada componente adicional pode aumentar a possibilidade de erro ou abuso. O objetivo do hardening é reduzir essa exposição sem interromper o uso legítimo do sistema.

Hardening é contínuo: sistemas mudam, recebem atualizações, ganham novas funções e passam a enfrentar novas ameaças.

## 2. Principais métodos

| Método | Como reduz riscos | Exemplo |
|---|---|---|
| **Reduzir a superfície de ataque** | Remove componentes, aplicações e serviços desnecessários. | Desativar um serviço que não faz parte da função do servidor. |
| **Usar uma baseline segura** | Define uma configuração esperada e ajuda a encontrar desvios. | Comparar um Windows Server com uma recomendação adequada à sua versão. |
| **Aplicar menor privilégio** | Limita o que cada usuário ou serviço pode acessar ou alterar. | Restringir o uso de contas administrativas às tarefas que exigem esses privilégios. |
| **Manter sistemas atualizados** | Corrige falhas conhecidas e mantém versões suportadas. | Aplicar atualizações após avaliar compatibilidade e validar o resultado. |
| **Restringir conexões de rede** | Limita quais redes e usuários podem alcançar cada serviço. | Permitir administração remota somente a partir de uma rede autorizada. |
| **Proteger dados e configurações** | Reduz acesso indevido a arquivos, segredos e informações sensíveis. | Limitar a leitura de arquivos de configuração que contêm credenciais. |
| **Monitorar e revisar** | Ajuda a identificar mudanças indevidas e desvios da configuração esperada. | Registrar alterações em contas privilegiadas e regras de firewall. |
| **Testar e controlar mudanças** | Reduz a chance de uma alteração interromper o serviço. | Testar a nova política em um sistema de teste e manter uma forma de reversão. |

### Princípios que orientam as decisões

- **Menor privilégio:** conceder apenas os acessos necessários.
- **Mínimo necessário:** manter apenas os serviços e aplicativos exigidos pela função do sistema.
- **Segurança por padrão:** bloquear o que não foi explicitamente autorizado.
- **Padronização:** documentar configurações para que sistemas equivalentes tenham comportamentos consistentes.
- **Revisão contínua:** revisar a configuração quando mudarem a função, o software ou as condições do ambiente.

## 3. Como analisar uma configuração

Use esta sequência para avaliar um servidor, estação de trabalho, navegador, aplicação ou serviço:

1. **Entenda a função:** para que o ativo é usado e quem é responsável por ele?
2. **Identifique o necessário:** quais contas, aplicações, serviços e conexões fazem parte dessa função?
3. **Escolha uma referência:** use uma baseline do fornecedor, um CIS Benchmark ou um padrão da organização compatível com o sistema.
4. **Compare com o estado observado:** registre o que está de acordo, o que diverge e o que ainda precisa de confirmação.
5. **Avalie o impacto:** que risco a mudança reduz? Que usuários ou serviços podem ser afetados?
6. **Proponha a mudança:** descreva a ação e como desfazê-la caso cause problema.
7. **Valide:** confirme a configuração e teste se a função legítima continua operando.
8. **Registre:** documente evidências, exceções e a data para uma nova revisão.

> Uma recomendação de benchmark é um ponto de partida. A configuração correta depende da função e do contexto do ativo.

## 4. Exemplos por ambiente

### Windows

- Revisar contas locais e grupos administrativos.
- Ativar e revisar as regras do firewall.
- Remover ou desativar serviços que não têm função aprovada.
- Restringir acesso remoto e interfaces administrativas.
- Manter sistema e aplicações em versões suportadas.
- Comparar as políticas com baselines de segurança compatíveis.

### Linux

- Instalar somente os pacotes necessários para a função do sistema.
- Revisar serviços ativos, portas e tarefas agendadas.
- Limitar contas privilegiadas e revisar permissões de arquivos.
- Restringir administração remota conforme a política do ambiente.
- Aplicar atualizações suportadas e manter registros úteis para auditoria.

### Navegadores e serviços

- Manter versões suportadas.
- Revisar extensões, permissões e componentes desnecessários.
- Restringir interfaces administrativas.
- Remover contas e configurações de demonstração.
- Proteger segredos e registrar alterações importantes.

## 5. Baselines, CIS Benchmarks e ISO

Uma **baseline** descreve a configuração esperada para uma classe de sistemas ou uma função específica. Ela permite comparar o estado atual com um padrão documentado.

Os **CIS Benchmarks** são guias técnicos com recomendações de configuração segura para diferentes tecnologias. Antes de usar um benchmark, confira se ele corresponde ao produto, à versão e à função do ativo.

A **ISO/IEC 27002:2022** oferece controles e orientações que ajudam a organizar o processo:

- **8.9 — Gestão de configuração:** estabelecer e revisar configurações.
- **8.8 — Gestão de vulnerabilidades técnicas:** identificar e tratar vulnerabilidades.
- **8.32 — Gestão de mudanças:** controlar alterações e seus possíveis impactos.

A ISO orienta a gestão de segurança, mas não é uma checklist técnica para cada sistema. O [NIST SP 800-40 Rev. 4](https://csrc.nist.gov/pubs/sp/800/40/r4/final) complementa o tema de atualizações e gestão de patches.

## 6. Ferramentas

| Ferramenta | Uso |
|---|---|
| **CIS Benchmarks** | Consultar recomendações de configuração segura. |
| **Microsoft Security Compliance Toolkit** | Comparar e analisar baselines de segurança Microsoft. |
| **Ubuntu Security Guide** | Auditar configurações de segurança em versões compatíveis do Ubuntu. |
| **Lynis** | Auditar sistemas Linux/Unix e identificar oportunidades de hardening. |
| **OpenSCAP** | Verificar sistemas contra perfis de segurança compatíveis. |
| **Greenbone/OpenVAS ou Nessus** | Avaliar vulnerabilidades em ativos autorizados. |

Uma ferramenta de auditoria ou scanner ajuda a encontrar itens que precisam de análise. O resultado precisa ser verificado no contexto do sistema e não garante, sozinho, que ele esteja seguro.

## 7. Atividade prática: auditoria e hardening de Linux

[Linux Privilege Primitives Audit — RangeWX](https://rangewx.com/en/labs/audit-privileges-linux-m1)

Na VM Debian disponibilizada pela plataforma, investigue as configurações de SUID/SGID, capabilities, AppArmor e serviços. Identifique as configurações inadequadas, diferencie problemas reais de falsos positivos e aplique as correções necessárias.

Ao final, documente os achados, justifique a prioridade das correções e escreva comandos para verificar se o sistema ficou mais seguro. A plataforma valida o estado final da VM.

A atividade exige uma conta gratuita e foca hardening de Linux, especialmente em permissões e privilégios.

## 8. Reflita

- O que significa reduzir a superfície de ataque?
- Quais três métodos de hardening você considera mais importantes para um servidor? Por quê?
- Por que uma recomendação de benchmark precisa ser avaliada antes de ser aplicada?
- Que evidências permitem confirmar que uma mudança melhorou a configuração?
- O que fazer quando uma recomendação não pode ser aplicada sem interromper uma função necessária?

## 9. Mensagens principais

- Hardening reduz a superfície de ataque por meio de configurações seguras e revisadas.
- Os métodos incluem baseline, remoção de serviços desnecessários, menor privilégio, atualizações, controle de rede, proteção de dados e monitoramento.
- A configuração adequada depende da função do ativo.
- Mudanças devem ser avaliadas, testadas, documentadas e verificadas.
- Benchmarks e ferramentas apoiam a análise, mas não substituem o julgamento técnico.

## Referências

- [ISO/IEC 27002:2022 — Information security controls](https://www.iso.org/standard/75652.html).
- [NIST SP 800-40 Rev. 4 — Guide to Enterprise Patch Management Planning](https://csrc.nist.gov/pubs/sp/800/40/r4/final).
- [CIS Benchmarks](https://www.cisecurity.org/cis-benchmarks).
- [Microsoft Security Compliance Toolkit](https://learn.microsoft.com/en-us/windows/security/operating-system-security/device-management/windows-security-configuration-framework/security-compliance-toolkit-10).
- [Ubuntu Security Guide](https://documentation.ubuntu.com/security/compliance/usg/).
- [Lynis Documentation](https://cisofy.com/documentation/lynis/).
- [OpenSCAP Base](https://www.open-scap.org/tools/openscap-base/).
- [Greenbone Community Edition Documentation](https://greenbone.github.io/docs/latest/).
- [HTB — How to Play Sherlocks](https://help.hackthebox.com/en/articles/8570249-how-to-play-sherlocks).
