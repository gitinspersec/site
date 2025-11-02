# Aula — Criptografia Avançada: Hashes, Autenticação e OAuth 2.0

Objetivo: Entender os fundamentos e aplicações práticas da criptografia moderna, incluindo funções de hash, armazenamento seguro de senhas, algoritmos modernos (SHA-256, Argon2, bcrypt) e padrões de autenticação segura como o OAuth 2.0 (RFC 6749).

---

## 1. Fundamentos da Criptografia Moderna

A criptografia moderna é baseada em **matemática aplicada** e tem três objetivos principais:

- **Confidencialidade**: Apenas as pessoas autorizadas podem acessar a informação.
- **Integridade**: Todos os dados permanecem inalterados durante a transmissão ou armazenamento.
- **Autenticação**: Verificar a identidade das partes envolvidas na comunicação.

### 1.1. Tipos de Criptografia

1. **Criptografia Simétrica:** Usa a mesma chave para criptografar e descriptografar.

2. **Criptografia Assimétrica:** Usa um par de chaves — pública e privada.

3. **Hash Criptográfico:** Função unidirecional que transforma dados em um valor fixo, irreversível.

---

## 2. Hash: Integridade e Autenticação

Funções de hash são amplamente utilizadas para **integridade de dados, assinaturas digitais e armazenamento seguro de senhas**.
Elas produzem uma sequência de tamanho fixo que representa a entrada original. Existem diversos modelos de hash, entre eles:

| Algoritmo   | Tamanho do Hash | Segurança                   | Status                       |
| :---------- | :-------------- | :-------------------------- | :--------------------------- |
| **MD5**     | 128 bits        | Fraco (colisões conhecidas) | Quebrado                     |
| **SHA-1**   | 160 bits        | Arcaico e desatualizado                    | Quebrado                     |
| **SHA-256** | 256 bits        | Forte                       | Seguro                       |
| **SHA-512** | 512 bits        | Muito forte                 | Seguro                       |
| **Argon2**  | Configurável    | Forte                       | Recomendado (hash de senhas) |

---

### 2.1. Propriedades Essenciais

1. Uma função de hash sempre gera o mesmo resultado quando recebe a mesma entrada.
2. Ela é projetada para ser unidirecional, tornando impossível recuperar os dados originais a partir do valor gerado.
3. É extremamente difícil encontrar duas entradas diferentes que resultem no mesmo hash.
4. Além disso, pequenas alterações na entrada produzem mudanças drásticas no resultado final.

---

### 2.2. Exemplos de Hashes com OpenSSL

#### SHA-256

```bash
echo -n "seguranca" | openssl dgst -sha256
```

#### SHA-512

```bash
echo -n "criptografia" | openssl dgst -sha512
```

#### MD5 (exemplo de hash quebrado)

```bash
echo -n "123456" | openssl dgst -md5
```

**Observação:**
MD5 permite encontrar duas mensagens diferentes que produzem o mesmo hash, tornando-o inútil e inseguro para integridade e autenticação. Assim, é recomendado utilizar **SHA-256**, **SHA-512** ou **algoritmos de derivação de chaves para senhas** (bcrypt, scrypt, Argon2) para garantir segurança adequada.

---

## 3. Hashes Quebrados: MD5 e SHA-1

O MD5, criado em 1992, foi quebrado em 2004 e permite gerar duas mensagens diferentes com o mesmo hash, o que o torna inútil e inseguro para integridade e autenticação.
O SHA-1, que o sucedeu, também teve colisões comprovadas em 2017 e foi abandonado.
Eles ainda aparecem em sistemas antigos e desatualizados, mas o ideal é sempre usar SHA-256, SHA-512 ou funções modernas como bcrypt, scrypt e Argon2 para garantir segurança adequada.

---

## 4. Hashes de Senhas e Derivação de Chaves

Hashing simples não é suficiente para proteger senhas.
É necessário adicionar **salt** (valor aleatório único) e aplicar o hash **milhares de vezes** — técnica chamada **Key Derivation Function (KDF)**.

| Algoritmo  | Recurso Principal                 | Vantagem                      | Qualidade             |
| :--------- | :-------------------------------- | :---------------------------- | :----------------------- |
| **bcrypt** | Iterativo            | Fácil de configurar           | Bom                      |
| **scrypt** | Usa memória e CPU                 | Dificulta ataques paralelos   | Muito bom                |
| **Argon2** | Memória-hard + tempo configurável | Melhor desempenho e segurança | Excelente (Padrão atual) |

---

### 4.1. Exemplo em Python (bcrypt)

```python
import bcrypt

senha = b"senha123"
hash = bcrypt.hashpw(senha, bcrypt.gensalt())
print(hash)
```

### 4.2. Exemplo em Python (Argon2)

```python
from argon2 import PasswordHasher

ph = PasswordHasher()
hash = ph.hash("senha123")
print(hash)
```

**Verificação:**

```python
ph.verify(hash, "senha123")
```

---

### 4.3. Comparativo Técnico

| Algoritmo | Resistência a GPU | Configuração de memória | Tempo ajustável | Padrão Moderno |
| :-------- | :---------------- | :---------------------- | :-------------- | :------------- |
| bcrypt    | Média             | Não                     | Sim             | Parcial        |
| scrypt    | Alta              | Sim                     | Sim             | Bom            |
| Argon2    | Muito alta        | Sim                     | Sim             | **Recomendado**  |

---

## 5. OAuth 2.0 (RFC 6749): Padrão de Autenticação e Autorização

O **OAuth 2.0** é um protocolo padronizado que permite a **autorização delegada** — um aplicativo pode agir em nome de um usuário sem precisar da senha dele.

### 5.1. Conceitos Fundamentais

| Termo                    | Descrição                                               |
| :----------------------- | :------------------------------------------------------ |
| **Resource Owner**       | Usuário que concede acesso a seus dados.                |
| **Client**               | Aplicação que solicita o acesso (ex: app de terceiros). |
| **Authorization Server** | Servidor que autentica o usuário e emite tokens.        |
| **Resource Server**      | API que contém os dados protegidos.                     |

---

### 5.2. Fluxos Principais (Grant Types)

| Tipo de Fluxo          | Uso Típico                     | Segurança           |
| :--------------------- | :----------------------------- | :------------------ |
| **Authorization Code** | Web Apps / Mobile              | Alta                |
| **Implicit**           | SPAs (legado)                  | Baixa (deprecated)  |
| **Client Credentials** | Comunicação entre servidores   | Alta                |
| **Password Grant**     | Legado (usuário fornece senha) | Inseguro (obsoleto) |

---

### 5.3. Exemplo de Fluxo Authorization Code

1. Um usuário acessa um app (Client).
2. O app redireciona para o Authorization Server.
3. O usuário autentica e autoriza.
4. O app recebe um authorization code temporário.
5. O app troca o código por um access token.
6. O token é usado para acessar recursos protegidos.

#### Exemplo (HTTP)

```bash
# Solicitação de token
curl -X POST https://auth.exemplo.com/token \
  -d "grant_type=authorization_code" \
  -d "code=abc123" \
  -d "redirect_uri=https://app.exemplo.com/callback" \
  -u "client_id:client_secret"
```

Resposta:

```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIs...",
  "token_type": "Bearer",
  "expires_in": 3600,
  "scope": "read_profile"
}
```

---

## 6. Boas Práticas

| Prática                            | Descrição                                        |
| :--------------------------------- | :----------------------------------------------- |
| **Usar Argon2 para senhas**        | Padrão atual de hash seguro.                     |
| **Evitar MD5/SHA-1**               | Algoritmos quebrados e inseguros.                |
| **TLS 1.3**                        | Use sempre comunicações criptografadas modernas. |
| **Rotação de chaves**              | Altere chaves periodicamente.                    |
| **Token expiração curta (OAuth2)** | Reduz impacto de roubo de token.                 |
| **Salts únicos e aleatórios**      | Evitam ataques de rainbow tables.                |

---

## 7. Ferramentas

| Ferramenta     | Uso                                       |
| :------------- | :---------------------------------------- |
| `openssl`      | Geração de hashes e certificados.         |
| `hashcat`      | Teste de força bruta (educacional).       |
| `argon2` (CLI) | Criação e verificação de hashes modernos. |
| `jwt.io`       | Visualizar tokens JWT de OAuth2.          |
| `Wireshark`    | Analisar fluxos OAuth2 e TLS.             |

---

## 8. **Resumo:**
>
> * Use SHA-2 (SHA-256/512) para integridade.
> * Use Argon2 para senhas.
> * Evite MD5 e SHA-1.
> * Use OAuth 2.0 com fluxos seguros e tokens curtos.
> * Monitore, atualize e audite regularmente.
