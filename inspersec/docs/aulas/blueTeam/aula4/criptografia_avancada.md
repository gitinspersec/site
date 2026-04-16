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

### 1.2. Criptografia Simétrica

A criptografia simétrica utiliza a mesma chave para encriptar e decriptar dados. É eficiente para grandes volumes de dados, mas o desafio principal é a distribuição segura da chave compartilhada.

#### Algoritmos Principais
- **AES (Advanced Encryption Standard)**: Padrão atual, com tamanhos de chave de 128, 192 ou 256 bits. Seguro e amplamente usado.
- **DES/3DES**: Obsoleto devido à chave pequena (56 bits) e vulnerabilidades.

#### Modos de Operação
- **ECB (Electronic Codebook)**: Simples, mas inseguro para padrões repetitivos.
- **CBC (Cipher Block Chaining)**: Usa vetor de inicialização (IV) para maior segurança.
- **GCM (Galois/Counter Mode)**: Fornece confidencialidade e integridade autenticada.

#### Exemplo com OpenSSL (AES-256-CBC)

```bash
# Encriptar um arquivo
openssl enc -aes-256-cbc -salt -in arquivo.txt -out arquivo.enc -k "minha_chave_secreta"

# Decriptar
openssl enc -d -aes-256-cbc -in arquivo.enc -out arquivo_dec.txt -k "minha_chave_secreta"
```

#### Exemplo em Python (com cryptography library)

```python
from cryptography.fernet import Fernet

# Gerar chave
chave = Fernet.generate_key()
cipher = Fernet(chave)

# Encriptar
dados = b"Dados secretos"
encriptado = cipher.encrypt(dados)
print(encriptado)

# Decriptar
decriptado = cipher.decrypt(encriptado)
print(decriptado.decode())
```

---

### 1.3. Criptografia Assimétrica

A criptografia assimétrica usa pares de chaves: uma pública (para encriptar/verificar) e uma privada (para decriptar/assinar). Resolve o problema de distribuição de chaves da simétrica, mas é mais lenta.

#### Algoritmos Principais
- **RSA**: Baseado em fatoração de números primos grandes. Usado para encriptação e assinaturas digitais. Chaves típicas: 2048-4096 bits.
- **ECC (Elliptic Curve Cryptography)**: Mais eficiente que RSA para o mesmo nível de segurança. Curvas como secp256r1 ou ed25519.
- **ed25519**: Uma variante de ECC otimizada para assinaturas digitais, usada em SSH, Git, e blockchains como Solana.

#### Exemplo: Geração de Chaves RSA com OpenSSL

```bash
# Gerar par de chaves
openssl genpkey -algorithm RSA -out private_key.pem -aes256 -pass pass:minha_senha
openssl rsa -pubout -in private_key.pem -out public_key.pem -passin pass:minha_senha

# Encriptar com chave pública
echo "Mensagem secreta" | openssl rsautl -encrypt -pubin -inkey public_key.pem -out mensagem.enc

# Decriptar com chave privada
openssl rsautl -decrypt -inkey private_key.pem -in mensagem.enc -passin pass:minha_senha
```

#### Exemplo: Assinatura Digital com ed25519 (Python com cryptography)

```python
from cryptography.hazmat.primitives.asymmetric import ed25519
from cryptography.hazmat.primitives import serialization

# Gerar chaves
private_key = ed25519.Ed25519PrivateKey.generate()
public_key = private_key.public_key()

# Assinar
mensagem = b"Mensagem para assinar"
assinatura = private_key.sign(mensagem)

# Verificar
try:
    public_key.verify(assinatura, mensagem)
    print("Assinatura válida")
except:
    print("Assinatura inválida")
```

**Vantagens de ed25519 sobre RSA**: Menor tamanho de chave (32 bytes vs. 2048+ bits), mais rápido, resistente a ataques de canal lateral.

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

## 6. JWT (JSON Web Tokens): Tokens para Autenticação

JWT é um padrão aberto (RFC 7519) para representar claims de forma segura entre partes. É amplamente usado em OAuth 2.0 para access tokens, mas também em sessões stateless e APIs.

### 6.1. Estrutura do JWT

Um JWT é composto de três partes separadas por pontos (`.`):

1. **Header**: Metadados como algoritmo de assinatura.
   ```json
   {
     "alg": "HS256",
     "typ": "JWT"
   }
   ```

2. **Payload**: Claims (dados) como user ID, expiração.
   ```json
   {
     "sub": "1234567890",
     "name": "João Silva",
     "iat": 1516239022,
     "exp": 1516242622
   }
   ```

3. **Signature**: Assinatura para verificar integridade.

O token completo: `header.payload.signature` (base64url encoded).

### 6.2. Algoritmos de Assinatura

- **HS256 (HMAC SHA-256)**: Simétrico, usa chave secreta compartilhada.
- **RS256 (RSA SHA-256)**: Assimétrico, usa chave privada para assinar, pública para verificar.
- **ES256 (ECDSA)**: Baseado em curvas elípticas, como ed25519.

**Atenção**: Evite "none" algorithm (sem assinatura) para evitar ataques.

### 6.3. Exemplo em Python (PyJWT)

```python
import jwt
import datetime

# Chave secreta
secret = "minha_chave_secreta"

# Criar payload
payload = {
    "user_id": 123,
    "exp": datetime.datetime.utcnow() + datetime.timedelta(hours=1)
}

# Assinar (HS256)
token = jwt.encode(payload, secret, algorithm="HS256")
print(token)

# Verificar
decoded = jwt.decode(token, secret, algorithms=["HS256"])
print(decoded)
```

### 6.4. Segurança e Boas Práticas

- Use HTTPS sempre.
- Defina expiração curta.
- Valide issuer e audience.
- Evite armazenar dados sensíveis no payload (JWT não é encriptado, apenas assinado).

---

## 7. Boas Práticas

| Prática                            | Descrição                                        |
| :--------------------------------- | :----------------------------------------------- |
| **Usar Argon2 para senhas**        | Padrão atual de hash seguro.                     |
| **Evitar MD5/SHA-1**               | Algoritmos quebrados e inseguros.                |
| **TLS 1.3**                        | Use sempre comunicações criptografadas modernas. |
| **Rotação de chaves**              | Altere chaves periodicamente.                    |
| **Token expiração curta (OAuth2)** | Reduz impacto de roubo de token.                 |
| **Salts únicos e aleatórios**      | Evitam ataques de rainbow tables.                |

---

## 8. Ferramentas

| Ferramenta     | Uso                                       |
| :------------- | :---------------------------------------- |
| `openssl`      | Geração de hashes e certificados.         |
| `hashcat`      | Teste de força bruta (educacional).       |
| `argon2` (CLI) | Criação e verificação de hashes modernos. |
| `jwt.io`       | Visualizar tokens JWT de OAuth2.          |
| `Wireshark`    | Analisar fluxos OAuth2 e TLS.             |

---

## 9. **Resumo:**
>
> * Use SHA-2 (SHA-256/512) para integridade.
> * Use Argon2 para senhas.
> * Evite MD5 e SHA-1.
> * Use OAuth 2.0 com fluxos seguros e tokens curtos.
> * Monitore, atualize e audite regularmente.
> * Para fundamentos matemáticos, veja [Fundamentos Matemáticos](criptografia_fundamentos_matematicos.md).
> * Para aplicações práticas, veja [Aplicações Práticas](criptografia_aplicada.md).

---

## 10. CTF - CryptoHack

Para praticar os conceitos aprendidos nesta aula, recomendamos o site [CryptoHack](https://cryptohack.org/), uma plataforma de CTF (Capture The Flag) focada em desafios de criptografia. Lá você encontrará exercícios interativos que ajudam a consolidar o conhecimento em hashes, criptografia simétrica e assimétrica, OAuth e muito mais.

Acesse: [https://cryptohack.org/](https://cryptohack.org/)
