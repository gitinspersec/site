# Exploração de Vulnerabilidades e o OWASP Top 10

*⏱️ Tempo de leitura: ~6 min*

> Até aqui você mapeou, interceptou e quebrou senhas. Agora você ataca a aplicação em si — fazendo ela executar o que você mandou.

## Pré-requisitos

- Ter feito as Aulas 1 a 5
- Saber subir o Juice Shop com Docker (Aula 4)

## O que é o OWASP Top 10

A **OWASP** é uma organização que cuida de segurança em aplicações. De tempos em tempos ela publica o **Top 10**: a lista das dez categorias de falha mais comuns e perigosas na web.

É o mapa que todo pentester de web usa pra saber onde procurar. Vale conhecer a lista inteira, mesmo que a gente só ataque algumas nesta aula:

| # | Categoria | O que é |
|---|-----------|---------|
| A01 | Controle de acesso quebrado | acessar o que não devia (dados de outro usuário, área de admin) |
| A02 | Falha de criptografia | dado sensível mal protegido (você viu na Aula 2) |
| A03 | **Injeção** | fazer o app executar seu input — **SQL Injection, XSS** |
| A04 | Design inseguro | a falha está na ideia, não no código |
| A05 | Configuração incorreta | serviço exposto, senha padrão, permissão larga |
| A06 | Componentes desatualizados | uma biblioteca velha com falha conhecida (CVE) |
| A07 | Falha de autenticação | login fraco, sessão mal feita (Aula 2) |
| A08 | Falha de integridade | confiar em código ou dado que não deveria |
| A09 | Falha de monitoramento | o ataque acontece e ninguém percebe |
| A10 | SSRF | fazer o servidor requisitar um endereço no seu lugar |

Repare que o curso já encostou em várias: autenticação e criptografia na Aula 2, configuração e serviços expostos nas Aulas 3 e 4. Hoje a gente foca na estrela da lista: **injeção**.

## Por que injeção é tão comum

Lembra da Aula 3: quase todo ataque web é uma **requisição HTTP manipulada**. Você controla o que manda — a URL, os parâmetros, o corpo.

A falha de injeção acontece quando o servidor pega esse seu input e **mistura com um comando** (uma consulta ao banco, um pedaço de HTML) sem separar o que é dado do que é instrução.

Aí você deixa de mandar um dado e passa a mandar um comando. As duas injeções mais famosas:

- **SQL Injection** — seu input vira parte da consulta ao **banco de dados**.
- **XSS** — seu input vira **JavaScript** que roda no navegador de outra pessoa.

As próximas duas páginas cobrem uma cada.

## O alvo: de volta ao Juice Shop

O laboratório é o mesmo **OWASP Juice Shop** da Aula 4 — uma loja cheia de falhas de propósito, feita pela própria OWASP pra treinar o Top 10. Suba com Docker:

```bash
docker run -d -p 3000:3000 bkimminich/juice-shop
```

Acesse `http://localhost:3000`. Cada desafio que você resolver aqui é uma categoria do Top 10 na prática.

## Próximos passos

Comece pela injeção mais clássica: a **[Injeção de SQL](sql-injection.md)** — fazer o banco de dados trabalhar pra você.
