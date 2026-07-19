# Autenticação

*⏱️ Tempo de leitura: ~7 min*

> Toda vez que você loga, o sistema precisa responder uma pergunta: "é você mesmo?". Esta página mostra como ele responde — e como um atacante engana essa resposta.

## Pré-requisitos

- Ter lido a página anterior, [Codificação, Criptografia e Hashing](codificacao-cripto-hashing.md)

## Autenticação x Autorização

Dois conceitos que vivem juntos e são fáceis de confundir:

- **Autenticação** — provar **quem** você é (o login).
- **Autorização** — depois de autenticado, decidir **o que** você pode fazer (as permissões).

Esta página é sobre a primeira.

## Os três fatores

Você prova quem é de três formas. Sistemas seguros combinam mais de uma:

| Fator | O que é | Exemplo |
|-------|---------|---------|
| Algo que você **sabe** | um segredo na sua cabeça | senha, PIN |
| Algo que você **tem** | um objeto com você | celular, token, chave SSH |
| Algo que você **é** | uma característica física | digital, rosto |

Combinar dois deles é a **autenticação de dois fatores (2FA)**: senha (*sabe*) + código no celular (*tem*). Assim, uma senha vazada sozinha não é suficiente pra entrar.

## O problema da web: o HTTP não lembra de você

O HTTP é **stateless**: cada requisição é independente e o servidor te esquece entre uma e outra. Então, depois que você loga, como o site sabe que a próxima requisição ainda é sua?

Ele te dá um **comprovante** que o navegador reenvia a cada requisição. Há dois modelos comuns: **sessão** e **JWT**.

## Sessão + cookie

1. Você loga. O servidor cria uma **sessão** e guarda ela do lado dele (em memória ou banco).
2. Ele te devolve um **cookie** com um **ID de sessão** — uma string aleatória.
3. O navegador reenvia esse cookie em toda requisição. O servidor lê o ID e sabe que é você.

O cookie de sessão é como uma pulseira de festa: não diz seu nome, mas prova que você já passou pela portaria.

> ⚠️ Se alguém copia o seu cookie de sessão, o servidor não percebe diferença — pra ele, o ladrão **é você**. Isso é o **roubo de sessão** (*session hijacking*), e é um dos CTFs desta aula. Uma das formas de roubar esse cookie é via XSS, que você vê na Aula 6.

## JWT (JSON Web Token)

O JWT é um modelo mais moderno. Em vez de guardar a sessão no servidor, o **próprio token carrega** quem você é — assinado pra ninguém conseguir adulterar.

Um JWT tem três partes separadas por ponto:

```
header.payload.signature

header     →  {"alg":"HS256"}       Base64 · qual algoritmo de assinatura
payload    →  {"user":"joe",...}    Base64 · quem você é e outros dados
signature  →  HMAC(dados, segredo)  o hash que prova que nada foi mexido
```

- **Header** e **payload** são só **Base64** — ou seja, qualquer um lê (lembra: Base64 não é segurança).
- **Signature** é um hash feito com um **segredo** que só o servidor conhece. É ela que garante que o token não foi alterado.

> 💡 No <a href="https://jwt.io/" target="_blank" rel="noopener noreferrer">jwt.io</a> você consegue ver um exemplo de tudo que foi explicado acima.

### Onde o JWT quebra

- Se o servidor não confere a assinatura direito (o clássico `alg: none`), dá pra **forjar** um token e virar admin.
- Se o **segredo** da assinatura é fraco (tipo `secret` ou `123456`), dá pra descobrir por força bruta e assinar tokens falsos você mesmo.

## Os dois modelos, lado a lado

| | Sessão | JWT |
|--|--------|-----|
| Onde fica o estado | no servidor | no próprio token |
| O cliente guarda | um ID no cookie | o token inteiro |
| Ataque clássico | roubar o cookie de sessão | forjar ou alterar o token |

## Para casa

Duas tarefas: a primeira é prática, a segunda prepara a próxima aula.

### Tarefa 1 — Decodifique e quebre

**a) Decodifique.** Cole a string abaixo no <a href="https://gchq.github.io/CyberChef/" target="_blank" rel="noopener noreferrer">CyberChef</a> e descubra a mensagem escondida:

```
SW5zcGVyU2Vje2NvZGlmaWNhcl9uYW9fZV9jcmlwdG9ncmFmYXJ9
```

**b) Quebre os hashes.** Os três MD5 abaixo são senhas comuns. Você vai usar o `john` (ou o `hashcat`) com a wordlist `rockyou.txt`:

```
f25a2fc72690b780b2a14e140ef6a9e0
0571749e2ac330a7455809c6b0e7af90
8621ffdbc5698829397d97767ac13db3
```

> ⚠️ No Kali (principalmente no WSL) a wordlist não vem instalada por padrão. Faça isto **uma vez**, antes:

```bash
sudo apt update && sudo apt install wordlists     # instala as wordlists
sudo gunzip /usr/share/wordlists/rockyou.txt.gz   # descompacta a rockyou (precisa de sudo)
```

Depois, salve os três hashes num arquivo `hashes.txt` e rode:

```bash
john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt
```

### Tarefa 2 — Leitura pra Aula 3

A próxima aula é de ferramentas (Burp, Gobuster) e assume que você entende como a web conversa. Leia:

- [Requisições HTTP e HTTPS](../../redTeam/aulasextra/http_https.md)
- [Protocolos e TCP — IP, portas e serviços](../../redTeam/aulasextra/protocolos_tcp.md)
