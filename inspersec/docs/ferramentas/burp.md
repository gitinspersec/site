## O que é?

O Burp Suite é uma ferramenta de segurança de aplicativos web que permite aos profissionais de segurança realizar uma ampla gama de testes de penetração.   
Com uma interface gráfica, ele atua como um proxy, permitindo interceptar e capturar as solicitações e respostas trocadas entre o navegador e a aplicação. 

## Usos

**Principais funcionalidades do Burp Suite:**   
- Interceptar e capturar solicitações e respostas entre o navegador e a aplicação  
- Modificar requests/responses em tempo real  
- Analisar o tráfego de rede  
- Realizar testes de segurança automatizados  
- Realizar ataques automatizados  
- Identificar pontos de entrada e parâmetros de interesse que podem ser suscetíveis a ataques  
- Identificar recursos ou informações confidenciais que podem ser acessadas por meio de URLs desprotegidos  
- Testar vulnerabilidades como XSS, SQL Injection, IDOR (Insecure Direct Object References), entre outras...  



## Como configurar?
### Instalação
Para o uso pessoal será usada a versão grátis da ferramenta: [Burp Suite Community Edition](https://portswigger.net/burp/communitydownload).

Após a instalação, crie um novo projeto e salve-o em algum lugar que você se lembre ou crie um projeto temporário na memória. Caso já tenha configurações procure o arquivo e inicialize a aplicação, caso contrário use o padrão da ferramenta.

### Configuração de rede
Dentro do Burp vá para "Proxy > Proxy Settings" e exporte o certificado no formato DER.  
  
No seu navegador procure as configurações de rede e configure o proxy manualmente (endereço: 127.0.0.1, porta: 8080) e instale o certificado para evitar erro de HTTPS.


## Interceptando Tráfego
Com o Burp Suite aberto vá em Proxy > Intercept > Intercept is on > Open Browser

A partir deste momento um navegador próprio da ferramenta será aberta para a interceptação de rotas. Este navegador já está pré-configurado para usar o proxy do Burp e confiar no certificado HTTPS da ferramenta.

Assim que uma requisição for feita, ela será interceptada e aparecerá na aba Proxy > Intercept. A partir daí você pode modificar qualquer parte da requisição (headers, corpo, cookies, etc...). Em seguida é possível clicar em Forward para enviar a requisição ao servidor ou clicar em Drop para cancelá-la.

### Visualizando a comunicação:
Vá em Proxy > HTTP history para ver todas as requisições e respostas que passaram pelo proxy. Cada entrada mostra detalhes como: URL, método, código de resposta, headers, corpo da requisição, tamanho e tempo de resposta.



## Diferença de burp-suit(proxy), postman, curl, nmap, entre outros...

| Ferramenta | Para que serve | Características principais |
|------------|----------------|-----------------------------|
| Burp Suite | Testes de segurança, interceptação e fuzzing | Intercepta tráfego, altera, analisa e testa |
|Postman|Testar APIs manualmente|Ótimo para desenvolvimento, não intercepta|
|curl|Cliente HTTP/S via terminal|Ideal para automação e testes simples|
|nmap|Scanner de rede|Descobre hosts, portas, serviços ativos|
|Wireshark|Sniffing de rede em baixo nível|Captura pacotes em qualquer protocolo|
|OWASP ZAP|Alternativa open-source ao Burp| Similar ao burp, mas com algumas limitações|