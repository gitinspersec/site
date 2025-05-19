# BurpSuite

## O que é o BurpSuite?

O Burp Suite é uma ferramenta de teste de segurança de aplicativos da web desenvolvida pela PortSwigger. Ele é usado para realizar testes manuais de segurança em aplicativos da web. O Burp Suite é composto por várias ferramentas que trabalham juntas para suportar todo o processo de teste de segurança de aplicativos da web, desde a fase inicial de mapeamento e análise de ataques até a exploração e pós-exploração.

Diferente das outras ferramentas que estudamos, o **Burp** é uma ferramenta com interface gráfica, o que facilita a visualização e a interação com os resultados dos testes.

O **Burp** funciona em todas as plataformas, para baixar a versão gratuita, acesse o [site oficial](https://portswigger.net/burp/communitydownload).

## Principais funcionalidades

O **Burp Suite** possui várias funcionalidades que o tornam uma ferramenta poderosa para testes de segurança de aplicativos da web. Algumas das principais funcionalidades incluem:

- **Proxy:** O **Burp Proxy** é uma ferramenta de interceptação de proxy que permite monitorar e modificar o tráfego entre o navegador e o servidor web. Ele é usado para analisar e modificar solicitações e respostas HTTP, permitindo identificar vulnerabilidades de segurança e realizar testes de segurança em aplicativos da web.

- **Scanner:** O **Burp Scanner** é uma ferramenta de varredura de segurança automatizada que identifica vulnerabilidades de segurança em aplicativos da web. Ele realiza varreduras de segurança em aplicativos da web em busca de vulnerabilidades comuns, como injeção de SQL, cross-site scripting (XSS) e falsificação de solicitação entre sites (CSRF).

- **Repeater:** O **Burp Repeater** é uma ferramenta de repetição de solicitações que permite reenviar solicitações HTTP para o servidor web. Ele é usado para testar e explorar vulnerabilidades de segurança em aplicativos da web, permitindo modificar e reenviar solicitações para analisar o comportamento do aplicativo.

- **Intruder:** O **Burp Intruder** é uma ferramenta de ataque de força bruta que automatiza ataques de força bruta em aplicativos da web. Ele é usado para testar a segurança de aplicativos da web, permitindo automatizar ataques de força bruta em parâmetros de solicitação, como senhas e tokens de sessão.


## O que é Força Bruta?

Força bruta é uma técnica usada para quebrar senhas ou chaves criptográficas por meio de tentativas repetidas. O atacante tenta todas as combinações possíveis de caracteres até encontrar a senha correta. O ataque de força bruta é um método de ataque comum usado por hackers para obter acesso não autorizado a sistemas e aplicativos.

Hoje em dia, a força bruta é uma técnica de ataque menos eficaz devido à complexidade das senhas e ao uso de medidas de segurança, como bloqueio de contas após várias tentativas de login malsucedidas. No entanto, ainda é importante estar ciente desse tipo de ataque e tomar medidas para proteger suas senhas e chaves criptográficas.

<!-- quote -->
> **Palavras do Maraba**: "Não consigo invadir, tentei tudo que sei e mesmo assim não consigo, vou ter que apelar"

## Analisando Requisições HTTP com o Burp Proxy

O **Burp Proxy** é uma ferramenta de interceptação de proxy que permite monitorar e modificar o tráfego entre o navegador e o servidor web. Ele é usado para analisar e modificar solicitações e respostas HTTP, permitindo identificar vulnerabilidades de segurança e realizar testes de segurança em aplicativos da web.

O que é possivel fazer com o proxy?

- **Interceptar e modificar solicitações HTTP:** O **Burp Proxy** permite interceptar e modificar solicitações HTTP entre o navegador e o servidor web. Isso permite analisar e modificar os parâmetros da solicitação, como cookies, cabeçalhos e parâmetros de URL.

- **Analisar solicitações e respostas HTTP:** O **Burp Proxy** permite analisar solicitações e respostas HTTP para identificar vulnerabilidades de segurança, como injeção de SQL, cross-site scripting (XSS) e falsificação de solicitação entre sites (CSRF).

- **SQL Injection:** O **Burp Proxy** pode ser usado para identificar vulnerabilidades de injeção de SQL em aplicativos da web. Ele permite analisar solicitações e respostas HTTP em busca de parâmetros vulneráveis que podem ser explorados para realizar ataques de injeção de SQL.

- **Cross-Site Scripting (XSS):** O **Burp Proxy** pode ser usado para identificar vulnerabilidades de cross-site scripting (XSS) em aplicativos da web. Ele permite analisar solicitações e respostas HTTP em busca de scripts maliciosos que podem ser injetados em páginas da web para roubar informações confidenciais dos usuários.

- **Falsificação de Solicitação entre Sites (CSRF):** O **Burp Proxy** pode ser usado para identificar vulnerabilidades de falsificação de solicitação entre sites (CSRF) em aplicativos da web. Ele permite analisar solicitações e respostas HTTP em busca de solicitações maliciosas que podem ser usadas para realizar ações não autorizadas em nome do usuário.

- **Header Manipulation and Injection:** O **Burp Proxy** permite manipular e injetar cabeçalhos HTTP em solicitações e respostas HTTP. Isso pode ser usado para testar a segurança de aplicativos da web e identificar vulnerabilidades de segurança relacionadas a cabeçalhos HTTP.

Vamos para a prática? Acesse o [site de testes](http://10.142.100.198:80). Vamos fazer um exemplo pratico de como usar