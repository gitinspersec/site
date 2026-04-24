# Projeto de Segurança Ofensiva
### Pesquisa, Exploração e Validação de Vulnerabilidades em Ambiente Controlado

---

## 1. Objetivo do Projeto

Este projeto tem como objetivo desenvolver nos membros a capacidade de investigar um ambiente desconhecido de forma autônoma. Mais do que executar técnicas, esperamos que cada grupo aprenda a **formular hipóteses, buscar informação por conta própria, testar abordagens e documentar o raciocínio** — construindo a mentalidade de quem trabalha com segurança ofensiva na prática.

A Metasploitable foi escolhida por se aproximar de um ambiente real mal configurado, diferente de um CTF onde existe uma resposta certa esperando para ser encontrada. Aqui, o grupo decide o que explorar, como explorar e o que isso significa.

### 1.1 O que buscamos em cada grupo

- **Autonomia** — o grupo deve ser capaz de decidir o que investigar sem depender de um roteiro fixo
- **Raciocínio documentado** — mais importante do que encontrar a vulnerabilidade é explicar por que foram buscar aquilo e o que esperavam encontrar
- **Processo real** — erros, tentativas frustradas e mudanças de direção fazem parte e devem estar no relatório
- **Comunicação técnica** — o relatório deve ser legível por outro membro do SEC que não participou do projeto

---

## 2. Estrutura do Projeto

### 2.1 Parte 1 — Pesquisa de Vulnerabilidade

Antes de tocar na máquina, o grupo escolhe uma vulnerabilidade para estudar em profundidade. O objetivo não é decorar — é **entender o suficiente para saber onde procurá-la e como reconhecê-la em um ambiente real**.

O grupo deve ser capaz de responder:

- Como essa vulnerabilidade funciona tecnicamente?
- Em que tipo de serviço ou configuração ela aparece?
- Como um atacante a identificaria em um ambiente desconhecido?
- Qual o impacto real se explorada?
- Existe algum caso real e importante relacionado? Se sim, qual e o que ele nos ensina?

---

#### Vulnerabilidades Sugeridas

Isso é um ponto de partida — o grupo é livre para buscar, documentar e explorar outras vulnerabilidades que desejar, mas sugerimos fortemente que sigam as opções abaixo por estarem disponíveis na Metasploitable.

- SQL Injection
- Cross-Site Scripting (XSS)
- Command Injection
- File Inclusion (LFI/RFI)
- Directory Traversal
- FTP Anonymous Login
- Weak Credentials
- Telnet Insecure Login
- SMB Misconfiguration
- Cronjob Misconfiguration
- PATH Hijacking

> ⚠️ **Importante:** conhecer onde a vulnerabilidade existe não significa saber explorá-la. O trabalho de pesquisa deve preparar o grupo para **raciocinar sobre o problema**, não apenas executar um exploit pronto.

---

### 2.2 Parte 2 — Reconhecimento da Metasploitable

O grupo tem acesso à máquina e começa a investigar **sem roteiro fixo**. A pergunta central é:

> *"O que esse servidor está expondo e o que isso significa?"*

Nessa etapa deixamos de lado temporariamente a vulnerabilidade estudada — em um ambiente real nunca sabemos por onde começar, e é exatamente essa habilidade que queremos desenvolver. O objetivo é que o grupo **explore, questione e documente o raciocínio por trás de cada decisão** — por que escolheram investigar determinada porta, serviço ou configuração.

---

#### Configuração do Ambiente

Antes de começar, cada grupo deve configurar a Metasploitable localmente seguindo o tutorial oficial do InsperSEC:

> 🔗 [Tutorial de instalação da Metasploitable](https://gitinspersec.github.io/site/Instalação/metasploitable2/)

A VM deve estar rodando em **pelo menos um notebook do grupo**. Toda a exploração ocorre nesse ambiente controlado — **nenhuma técnica deve ser aplicada fora dele**.

---

#### Reconhecimento Inicial

Em um pentest real, o reconhecimento é o que separa um atacante que age às cegas de um que age com estratégia. Como ponto de partida, o grupo deve buscar e documentar as seguintes informações:

1. Informações do sistema — OS, versão do kernel, arquitetura
2. Usuários, grupos privilegiados e sessões ativas
3. Serviços em execução e portas abertas
4. Binários SUID/SGID e capabilities configuradas
5. Permissões sudo e cronjobs ativos

> ⚠️ **Importante:** não basta listar o que foi encontrado — o relatório deve explicar **o que cada informação significa** e **quais caminhos ela abre** para as próximas etapas. Um dado sem análise não tem valor em segurança ofensiva.

---

#### Escalada de Privilégios

Com o reconhecimento concluído, o grupo agora tem informações suficientes para pensar estrategicamente. O próximo passo é **identificar e documentar 3 formas distintas de escalar privilégios** dentro do sistema.

Para cada forma encontrada, o relatório deve conter:

- **Vetor identificado** — o que foi encontrado e por que chamou atenção
- **Raciocínio** — como o grupo concluiu que aquilo era explorável
- **Execução** — prints dos comandos utilizados e resultado obtido
- **Impacto** — o que um atacante real conseguiria fazer com esse acesso

> 💡 **Dica de mentalidade:** as informações coletadas no reconhecimento inicial são o seu mapa. Binários SUID, cronjobs mal configurados, permissões sudo indevidas — tudo isso são pistas, não respostas prontas. O trabalho do grupo é conectar os pontos.

> ⚠️ **Importante:** não é necessário explorar as 3 formas com sucesso — uma tentativa bem documentada e analisada tem mais valor do que um resultado sem explicação.

---

### 2.3 Parte 3 — Aplicação da Vulnerabilidade

Chegou o momento de ligar os pontos.

Com o conhecimento construído na pesquisa teórica e a visão real do sistema obtida no reconhecimento, o grupo agora tem as ferramentas para responder a pergunta mais importante do projeto:

> *"A vulnerabilidade que estudei existe na Metasploitable — e consigo explorá-la?"*

Essa etapa é o fechamento natural de todo o processo. O grupo deve **encontrar e explorar dentro da própria Metasploitable** a vulnerabilidade pesquisada na Parte 1 — não se trata de executar um exploit encontrado na internet, mas de aplicar o raciocínio desenvolvido ao longo do projeto para identificar, confirmar e explorar a vulnerabilidade no ambiente real da VM.

---

#### O que esperamos

Para cada vulnerabilidade explorada, o relatório deve documentar:

- **Como foi identificada na Metasploitable** — o que no reconhecimento levou o grupo até ela
- **Intuito** — o que o grupo esperava conseguir antes de executar
- **Execução** — prints dos comandos utilizados com contexto
- **Resultado** — o que aconteceu, esperado ou não
- **Análise** — o que isso significa no contexto real do sistema

> ⚠️ **Importante:** toda a exploração deve ocorrer exclusivamente dentro da Metasploitable rodando localmente. Nenhuma técnica deve ser aplicada fora desse ambiente controlado.

> Deve ser exploradas no mínimo 1 vunerabilidade não relacionada diretamente com escalonamento de privilégios, com foco na vunerabilidade estudada na parte 1.

> 💡 **Lembre-se:** um resultado negativo bem documentado e analisado é tão válido quanto um exploit bem-sucedido. O que não tem valor é executar sem entender.

---

## 3. Conclusão e Entregável Final

O entregável do projeto é um **relatório único** cobrindo todo o processo — do estudo inicial à exploração final. Mais do que um registro do que foi feito, o relatório deve contar a história do raciocínio do grupo: o que pensaram, o que tentaram, o que funcionou e o que não funcionou.

---

### Estrutura do Relatório

**1. Introdução**
Apresentação do grupo, da vulnerabilidade escolhida e da motivação pela escolha.

**2. Pesquisa da Vulnerabilidade**
Documentação completa da Parte 1 — funcionamento técnico, contexto de aparição, impacto real e caso histórico relevante.

**3. Reconhecimento da Metasploitable**
Documentação completa da Parte 2 — informações coletadas, análise de cada dado encontrado e raciocínio que guiou as decisões.

**4. Escalada de Privilégios**
As 3 formas identificadas, com vetor, raciocínio, execução e impacto de cada uma.

**5. Aplicação da Vulnerabilidade**
Documentação completa da Parte 3 — como a vulnerabilidade foi encontrada na Metasploitable, intuito, execução com prints e análise do resultado.

**6. Conclusão**
Reflexão honesta sobre o processo — o que o grupo aprendeu, o que faria diferente e quais perguntas o projeto deixou em aberto.

---

### Qualidade esperada no relatório

Um bom relatório no InsperSEC não é uma lista de prints com legendas. É um documento que outra pessoa da entidade consegue ler e **entender completamente o que foi feito e por quê**.

> ⚠️ **Prints sem contexto não têm valor.** Cada imagem deve estar acompanhada de explicação do intuito, do comando utilizado e do que o resultado significa.

---

## 4. Avaliação

Este projeto será avaliado pela **qualidade do raciocínio documentado**, não pela perfeição dos resultados. O que nos importa é o quanto cada grupo conseguiu avançar de forma autônoma — entender o que estava buscando, por que estava buscando e o que encontrou.

**Não ligamos se:**
- O grupo não conseguiu explorar a vulnerabilidade com sucesso
- O relatório foi escrito com auxílio de ferramentas com IA
- O caminho percorrido teve erros e tentativas frustradas

**Ligamos muito se:**
- O raciocínio por trás de cada decisão não está documentado
- Os prints existem mas não há explicação do intuito e do resultado
- O grupo executou comandos sem entender o que estava fazendo


> ⚠️ O projeto deve ser concluído até 06/05 antes das 23:59.

**Como enviar:**
- Entrar no site https://www.jrtalents.com.br/talent
<br>
- Criar uma conta
<br>
- Entrar no desafio Projeto Red Team
<br>
- Anexar o pdf com a entrega do projeto
<br>