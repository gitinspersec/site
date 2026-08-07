# Ética e Engenharia Social

*⏱️ Tempo de leitura: ~8 min*

> Um funcionário clica no link errado, e nenhum firewall percebe. Esta aula é sobre esse alvo: as pessoas.

## Pré-requisitos

- Ter feito as Aulas 1 a 4
- Nenhum conhecimento técnico novo — aqui o "exploit" é psicológico

## O que é engenharia social

Engenharia social é **manipular alguém a fazer algo** que abre uma brecha: entregar uma senha, clicar num link, deixar você entrar na sala.

Em vez de quebrar o sistema, você convence a pessoa que tem acesso a ele. Quase sempre é mais barato que achar uma falha técnica.

Funciona porque explora gatilhos que todo mundo tem:

| Gatilho | Como o atacante usa |
|---------|---------------------|
| **Confiança** | se passa por alguém conhecido (banco, TI, um colega) |
| **Autoridade** | "é uma ordem do diretor", "aqui é da segurança" |
| **Urgência** | "sua conta será bloqueada em 1 hora" — pra você agir sem pensar |
| **Empatia** | "preciso muito de ajuda, você é o único que pode" |

## Os tipos que você vai encontrar

Quase todo golpe é uma variação de poucos formatos:

| Tipo | O que é |
|------|---------|
| **Phishing** | e-mail falso em massa (ex.: "seu banco detectou uma atividade suspeita") |
| **Spear phishing** | phishing sob medida pra uma pessoa específica (usa seu nome, seu cargo) |
| **Whaling** | spear phishing mirando um "peixe grande" (CEO, diretor financeiro) |
| **Vishing** | golpe por ligação (o falso "suporte técnico") |
| **Smishing** | golpe por SMS ("você ganhou um prêmio, clique aqui") |
| **Pretexting** | inventa um pretexto pra pedir dados ("aqui é o TI, preciso confirmar sua senha") |
| **Baiting** | deixa uma isca — um pendrive infectado "esquecido" no estacionamento |
| **Tailgating** | entra numa área restrita atrás de alguém autorizado |

> 💡 Repare que a maioria combina os gatilhos de cima. Um bom phishing é autoridade (parece o banco) + urgência (sua conta vai ser bloqueada).

## Dois casos reais

- **Fraude da fatura falsa (2013–2015).** Um lituano mandou faturas falsas se passando por um fornecedor real da Google e da Facebook. As duas pagaram. Prejuízo somado: mais de **US$ 100 milhões**. Nenhum sistema foi invadido — só e-mails bem feitos.
- **Golpe do Twitter (2020).** Um grupo ligou pra funcionários do Twitter se passando pelo time interno de TI e convenceu alguns a dar acesso às ferramentas de administração. Com isso, sequestraram as contas de Obama, Elon Musk e outras figuras pra aplicar um golpe de Bitcoin. Foi tudo por telefone e conversa, sem explorar nenhuma falha técnica.

## Hands-on: OSINT com o Sherlock

Todo ataque direcionado começa com **OSINT** (*Open Source Intelligence*): juntar o que já está público sobre o alvo. Quanto mais você sabe, mais convincente fica o golpe.

O **Sherlock** procura um nome de usuário em centenas de sites de uma vez e mostra onde ele existe. É como um alvo espalha a mesma identidade pela internet sem perceber.

## Instalação do Sherlock Usando o pipx

Para instalar o Sherlock com o **pipx**, siga os passos abaixo:

### 1. Instalar o pipx

O **pipx** é uma ferramenta que permite a instalação de aplicativos Python em ambientes virtuais isolados, garantindo que as dependências não afetem outras partes do sistema. Para instalar o pipx:

```bash
python3 -m pip install --user pipx
python3 -m pipx ensurepath
```

### 2. Instalar o Sherlock

Agora, com o pipx instalado, você pode instalar o Sherlock diretamente executando o comando:

```bash
pipx install sherlock-project
```

Este comando instalará o Sherlock e o tornará disponível para uso em seu terminal.
Rodar é simples — passe um nome de usuário:

```bash
sherlock nome_de_usuario
```

Ele lista cada site onde aquele usuário aparece. Alguns parâmetros úteis:

| Parâmetro | O que faz |
|-----------|-----------|
| `--site twitter` | procura só num site específico |
| `--json saida.json` | salva o resultado num arquivo |

## Como um phishing é montado

Ferramentas como o **GoPhish** e o **Zphisher** clonam uma página de login real (a do Instagram, a de um banco) em poucos minutos e capturam o que a vítima digita. O GoPhish ainda dispara os e-mails e mostra um painel: quantos abriram, quantos clicaram, quantos caíram.

Se uma página de login falsa idêntica à real leva 5 minutos pra existir, não dá pra confiar num login que chegou por link. Abra o site você mesmo, digitando o endereço.

## Como se defender e reconhecer

Sinais clássicos de um ataque de engenharia social:

- Um pedido que você **não esperava**, com **pressa** ("faça agora").
- Erros de português ou um endereço de e-mail estranho por trás de um nome conhecido.
- Um link cujo endereço real (passe o mouse por cima, sem clicar) não bate com o texto.
- Alguém pedindo algo que ninguém legítimo pediria — sua senha, um código do SMS.

O que reduz o risco:

- **2FA ligado** em tudo (lembra da Aula 2): uma senha vazada sozinha não basta.
- **Confirme por outro canal.** Recebeu um pedido estranho do "chefe"? Ligue pra ele.
- **Menos exposição.** Quanto menos dado seu está público, menos munição o atacante tem.

## Para casa

### Tarefa — Faça OSINT em você mesmo

Instale o Sherlock e rode contra um **nome de usuário seu** (um que você usa em vários sites):

```bash
sherlock seu_usuario
```

Veja em quantos sites ele aparece. Depois responda pra você: um estranho, olhando só isso, montaria um phishing convincente contra você? O que dava pra esconder?

## Próximos passos

Você viu o ataque que não precisa de código. A **[Aula 6](../aula6/owasp.md)** volta pro lado técnico e mais famoso do red team: explorar falhas em aplicações web — o OWASP Top 10, injeção de SQL, XSS e o malware que entra depois.
