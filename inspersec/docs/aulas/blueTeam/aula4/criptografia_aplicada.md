# Aplicações Práticas da Criptografia

Objetivo: Explorar implementações práticas de criptografia em cenários reais, com foco em utilidades como JWT, OAuth e desafios de CTF.

---

## 1. JWT em APIs para Autenticação Stateless

### Exemplo com Flask (Python)

```python
from flask import Flask, request, jsonify
import jwt
import datetime

app = Flask(__name__)
SECRET_KEY = "chave_secreta"

@app.route('/login', methods=['POST'])
def login():
    # Simular autenticação
    user = {"id": 1, "username": "user"}
    token = jwt.encode({
        "user_id": user["id"],
        "exp": datetime.datetime.utcnow() + datetime.timedelta(hours=1)
    }, SECRET_KEY, algorithm="HS256")
    return jsonify({"token": token})

@app.route('/protected', methods=['GET'])
def protected():
    token = request.headers.get('Authorization').split()[1]
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=["HS256"])
        return jsonify({"message": "Acesso permitido", "user_id": payload["user_id"]})
    except:
        return jsonify({"error": "Token inválido"}), 401

if __name__ == '__main__':
    app.run()
```

**Utilidade**: Implementar autenticação sem estado em APIs web, comum em microserviços.

---

## 2. OAuth 2.0 com Google para Login Social

### Fluxo Básico

1. Registrar app no Google Developer Console.
2. Redirecionar usuário para: `https://accounts.google.com/oauth/authorize?client_id=YOUR_ID&redirect_uri=YOUR_URI&scope=email&response_type=code`
3. Trocar código por token: POST para `https://oauth2.googleapis.com/token`
4. Usar token para acessar APIs.

### Exemplo em Python (requests)

```python
import requests

# Trocar código por token
data = {
    "client_id": "YOUR_CLIENT_ID",
    "client_secret": "YOUR_SECRET",
    "code": "AUTH_CODE",
    "grant_type": "authorization_code",
    "redirect_uri": "YOUR_URI"
}
response = requests.post("https://oauth2.googleapis.com/token", data=data)
token = response.json()["access_token"]

# Acessar API
headers = {"Authorization": f"Bearer {token}"}
user_info = requests.get("https://www.googleapis.com/oauth2/v2/userinfo", headers=headers)
print(user_info.json())
```

**Utilidade**: Integrar login social em aplicações web, delegando autenticação a provedores confiáveis.

---

## 3. Desafio CTF: Quebrando um Hash Simples

### Cenário
Você encontrou um hash MD5 em um CTF: `5d41402abc4b2a76b9719d911017c592` (corresponde a "hello").

### Solução com hashcat

```bash
# Instalar hashcat se necessário
# Usar wordlist rockyou.txt
hashcat -m 0 -a 0 5d41402abc4b2a76b9719d911017c592 /path/to/rockyou.txt
```

**Utilidade**: Prática em quebra de hashes fracos para entender vulnerabilidades em CTFs de segurança.

---

## 4. Análise de Tráfego Criptografado com Wireshark

Wireshark é essencial para inspecionar protocolos criptografados como TLS.

### Exemplo: Capturar Handshake TLS

1. Abra Wireshark e selecione interface de rede.
2. Filtre por `tls.handshake` para ver negociações.
3. Acesse um site HTTPS; veja Client Hello, Server Hello, troca de certificados.

### Exemplo: Decriptar TLS (se tiver chave privada)

- Configure Wireshark: Edit > Preferences > Protocols > TLS > (Pre)-Master-Secret log filename.
- Use `SSLKEYLOGFILE` no navegador para log de chaves.
- Filtre por `http` para ver tráfego decriptado.

**Utilidade**: Depurar conexões seguras, detectar ataques como downgrade.

---

## 5. Obtendo Certificado SSL de um Site

Certificados SSL garantem identidade e encriptação em HTTPS.

### Usando OpenSSL

```bash
# Obter certificado do site
openssl s_client -connect example.com:443 -servername example.com < /dev/null | openssl x509 -text -noout
```

### Usando Navegador

1. Clique no cadeado na barra de endereço.
2. Selecione "Certificado" ou "Ver certificado".
3. Veja detalhes: emissor, válido até, algoritmo.

### Verificação

- Use `openssl verify` para checar cadeia de confiança.
- Sites como SSL Labs (ssllabs.com) para análise completa.

**Utilidade**: Verificar segurança de sites, detectar certificados expirados ou fraudulentos.

---

## Resumo

Foque em utilidades práticas como autenticação (JWT/OAuth) e desafios (CTF). Evite hashes quebrados em produção e sempre use bibliotecas atualizadas.