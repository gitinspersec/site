# Requisições HTTP e HTTPS

## O que é HTTP?

O **HTTP** (HyperText Transfer Protocol) é o protocolo de comunicação utilizado para transferir dados na web. Toda vez que você acessa um site, seu navegador envia uma **requisição HTTP** ao servidor, que responde com o conteúdo solicitado (HTML, imagens, JSON, etc.).

O HTTP funciona no modelo **cliente-servidor**:

1. O **cliente** (navegador, `curl`, script Python) envia uma requisição
2. O **servidor** processa e retorna uma resposta

---

## Estrutura de uma Requisição HTTP

Uma requisição HTTP é composta por:

```
MÉTODO /caminho HTTP/1.1
Host: exemplo.com
User-Agent: Mozilla/5.0
Accept: text/html
Content-Type: application/json

{ "dados": "do corpo da requisição" }
```

### Componentes

| Componente     | Descrição                                                        |
|----------------|------------------------------------------------------------------|
| **Método**     | Ação desejada (GET, POST, PUT, DELETE, etc.)                     |
| **Caminho**    | Recurso no servidor (`/api/users`, `/login`, `/index.html`)      |
| **Headers**    | Metadados da requisição (tipo de conteúdo, autenticação, etc.)   |
| **Body**       | Corpo da requisição (presente em POST, PUT — dados enviados)     |

---

## Métodos HTTP (Verbos)

Os principais métodos HTTP e seus usos:

| Método     | Descrição                                   | Exemplo de Uso                     |
|------------|---------------------------------------------|------------------------------------|
| `GET`      | Buscar/ler dados                            | Acessar uma página, listar itens   |
| `POST`     | Enviar/criar dados                          | Formulário de login, criar conta   |
| `PUT`      | Atualizar dados (substitui o recurso)       | Atualizar perfil do usuário        |
| `DELETE`   | Remover dados                               | Deletar uma postagem               |
| `PATCH`    | Atualizar dados parcialmente                | Alterar apenas o e-mail do usuário |
| `HEAD`     | Igual ao GET, mas retorna apenas os headers | Verificar se recurso existe        |
| `OPTIONS`  | Verifica métodos permitidos pelo servidor   | Pré-flight de requisições CORS     |

---

## Status Codes (Códigos de Resposta)

O servidor responde com um código numérico indicando o resultado:

| Faixa   | Categoria         | Exemplos                                              |
|---------|--------------------|-------------------------------------------------------|
| `1xx`   | Informacional      | `100 Continue`                                        |
| `2xx`   | Sucesso            | `200 OK`, `201 Created`, `204 No Content`             |
| `3xx`   | Redirecionamento   | `301 Moved Permanently`, `302 Found`                  |
| `4xx`   | Erro do Cliente    | `400 Bad Request`, `401 Unauthorized`, `403 Forbidden`, `404 Not Found` |
| `5xx`   | Erro do Servidor   | `500 Internal Server Error`, `502 Bad Gateway`        |

> **Dica de Red Team**: Em pentesting, status codes como `401`, `403` e `500` podem revelar informações valiosas sobre a aplicação e suas proteções.

---

## Estrutura de uma Resposta HTTP

```
HTTP/1.1 200 OK
Content-Type: text/html; charset=UTF-8
Set-Cookie: session=abc123; HttpOnly
Content-Length: 1234

<html>
  <body>Conteúdo da página</body>
</html>
```

## O que é HTTPS?

O **HTTPS** (HTTP Secure) é a versão segura do HTTP. Ele utiliza **TLS** (Transport Layer Security) para criptografar a comunicação entre cliente e servidor.

### Por que HTTPS importa?

- **Sem HTTPS (HTTP puro):** os dados trafegam em texto claro — qualquer pessoa na mesma rede pode interceptar senhas, cookies e dados sensíveis.
- **Com HTTPS:** os dados são criptografados de ponta a ponta.

### Como funciona o TLS Handshake

O processo de estabelecimento de conexão segura:

1. **Client Hello** — Cliente envia versões TLS suportadas e cipher suites
2. **Server Hello** — Servidor escolhe a cipher suite e envia seu certificado digital
3. **Verificação** — Cliente valida o certificado (cadeia de confiança, validade, domínio)
4. **Troca de chaves** — Ambos geram uma chave de sessão simétrica
5. **Conexão segura** — Toda comunicação passa a ser criptografada

> Em Red Team, é possível realizar ataques **Man-in-the-Middle (MitM)** quando o cliente não valida corretamente os certificados ou quando o atacante consegue instalar um certificado falso no dispositivo da vítima.

---

## Utilizando o `curl`

O **curl** é uma ferramenta de linha de comando para enviar requisições HTTP diretamente do terminal. É muito utilizado em pentesting, automação e debugging de APIs.

### Sintaxe Básica

```bash
curl [opções] <URL>
```

### Opções Mais Utilizadas

| Opção         | Descrição                                                |
|---------------|----------------------------------------------------------|
| `-v`          | Modo verbose — mostra headers de requisição e resposta   |
| `-X <MÉTODO>` | Define o método HTTP (POST, PUT, DELETE, etc.)           |
| `-H`          | Adiciona um header customizado                           |
| `-d`          | Envia dados no body da requisição                        |
| `-I`          | Retorna apenas os headers da resposta (HEAD)             |
| `-L`          | Segue redirecionamentos (301, 302)                       |
| `-b`          | Envia cookies junto com a requisição                     |
| `-k`          | Ignora erros de certificado SSL (útil em labs)           |
| `-o <arquivo>`| Salva a resposta em um arquivo                           |
| `-s`          | Modo silencioso (sem barra de progresso)                 |

### Exemplos

#### GET simples (com verbose)

```bash
curl -v https://httpbin.org/get
```

O flag `-v` mostra o handshake TLS, os headers enviados e os headers de resposta — muito útil para análise.

#### POST com JSON

```bash
curl -X POST https://httpbin.org/post \
  -H "Content-Type: application/json" \
  -d '{"usuario": "admin", "senha": "123456"}'
```

#### POST com dados de formulário

```bash
curl -X POST https://httpbin.org/post \
  -d "username=admin&password=s3nh4"
```

#### Ver apenas os headers da resposta

```bash
curl -I https://httpbin.org/get
```

Útil para identificar o servidor (`Server`), tecnologias (`X-Powered-By`) e configurações de segurança.

#### Seguir redirecionamentos

```bash
curl -L https://httpbin.org/redirect/3
```

#### Enviar cookies manualmente

```bash
curl -b "session=abc123; token=xyz" https://httpbin.org/cookies
```

#### Ignorar verificação de certificado SSL

```bash
curl -k https://alvo-lab.local/api/dados
```

> **Atenção**: o flag `-k` ignora erros de certificado. Use apenas em ambientes controlados (labs, CTFs).

#### Simular um User-Agent diferente

```bash
curl -H "User-Agent: Mozilla/5.0 (Windows NT 10.0)" https://httpbin.org/user-agent
```

#### Salvar resposta em arquivo

```bash
curl -o resposta.html https://exemplo.com
```

---

## Relação com Segurança Ofensiva

Entender HTTP/HTTPS é fundamental para praticamente todas as técnicas de Red Team:

| Técnica                  | Como HTTP se aplica                                         |
|--------------------------|-------------------------------------------------------------|
| **SQL Injection**        | Injeção via parâmetros GET/POST                             |
| **XSS**                  | Payload inserido em parâmetros refletidos na resposta        |
| **CSRF**                 | Forjar requisições autenticadas usando cookies da vítima     |
| **Directory Bruteforce** | Enviar milhares de requisições GET testando caminhos         |
| **API Exploitation**     | Manipular verbos HTTP, headers e body para abusar de APIs   |

---
