# Requisições HTTP e HTTPS

*⏱️ Tempo de leitura: ~6 min*

> Material de preparo pra Aula 3. Toda ferramenta web (Burp, Gobuster) mexe com requisições HTTP — vale entender como elas funcionam antes.

## Pré-requisitos

- Ter feito a [Aula 1](../aula1/fundamentos.md) e lido a [Aula 2](../aula2/autenticacao.md) (a parte de sessão e cookies ajuda aqui)

## O que é HTTP

O **HTTP** é o protocolo que o navegador usa pra conversar com um site. É **cliente-servidor**:

1. o **cliente** (navegador, `curl`, um script) manda uma **requisição**;
2. o **servidor** processa e devolve uma **resposta**.

Lembra da Aula 2: o HTTP é *stateless*, e é por isso que existem cookies e sessão.

## Anatomia de uma requisição

```
POST /login HTTP/1.1
Host: exemplo.com
Content-Type: application/json

{ "usuario": "admin", "senha": "123456" }
```

| Parte | O que é |
|-------|---------|
| **Método** | a ação desejada (GET, POST, ...) |
| **Caminho** | o recurso (`/login`, `/api/users`) |
| **Headers** | metadados (tipo de conteúdo, autenticação, cookies) |
| **Body** | os dados enviados (em POST/PUT) |

## Métodos

| Método | Pra quê |
|--------|---------|
| `GET` | ler dados (abrir página, listar) |
| `POST` | enviar/criar (login, cadastro) |
| `PUT` / `PATCH` | atualizar (o recurso inteiro / em parte) |
| `DELETE` | remover |
| `HEAD` | igual ao GET, mas só os headers |

## Status codes

A resposta vem com um código de três dígitos:

| Faixa | Significa | Exemplos |
|-------|-----------|----------|
| `2xx` | deu certo | `200 OK`, `201 Created` |
| `3xx` | redirecionamento | `301`, `302` |
| `4xx` | erro do cliente | `401 Unauthorized`, `403 Forbidden`, `404 Not Found` |
| `5xx` | erro do servidor | `500`, `502` |

> 💡 Em pentest, os `4xx` e `5xx` entregam informação: um `403` num caminho diz "existe algo aqui que eu não posso ver", e um `500` pode revelar um erro interno explorável.

## HTTPS: o HTTP com cadeado

O **HTTPS** é o HTTP dentro de uma camada de criptografia, o **TLS**. Sem ele, tudo trafega em texto claro — qualquer um na mesma rede lê suas senhas e cookies.

No início da conexão acontece o **TLS handshake**:

1. cliente e servidor combinam versões e cifras;
2. o servidor manda seu **certificado**;
3. o cliente valida o certificado;
4. os dois geram uma **chave de sessão** e passam a usar criptografia simétrica.

Repare que é a Aula 2 de novo: **assimétrica** pra combinar a chave, **simétrica** pra transmitir os dados.

> ⚠️ Quando o cliente não valida o certificado direito, dá pra fazer um ataque **Man-in-the-Middle** (interceptar no meio da conversa). É assim que o **Burp** vai ler o seu tráfego HTTPS na Aula 3 — ele se coloca no meio com um certificado próprio.

## curl: requisições pelo terminal

O `curl` manda requisições HTTP direto do terminal. Você vai usar bastante.

```bash
curl -v https://httpbin.org/get           # -v mostra headers e handshake
curl -X POST https://httpbin.org/post \
  -H "Content-Type: application/json" \
  -d '{"usuario":"admin"}'                 # POST com dados
```

| Opção | O que faz |
|-------|-----------|
| `-v` | verbose (mostra tudo) |
| `-X` | define o método |
| `-H` | adiciona um header |
| `-d` | envia dados no body |

## Por que isso importa no ataque

Quase todo ataque web é uma requisição HTTP manipulada:

| Técnica | O que se manipula |
|---------|-------------------|
| SQL Injection | parâmetros de GET/POST |
| XSS | dados que voltam refletidos na resposta |
| Directory brute force | milhares de GET testando caminhos (Gobuster) |
| Roubo de sessão | o cookie que vai nos headers |

Na Aula 3 você faz isso na prática com Burp e Gobuster.
