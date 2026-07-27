# Injeção de SQL (SQL Injection)

*⏱️ Tempo de leitura: ~7 min*

> Um campo de login espera um e-mail. E se, em vez do e-mail, você escrever um pedaço de comando que o banco de dados vai obedecer?

## Pré-requisitos

- Ter lido a [página anterior](owasp.md) (o que é injeção)
- Juice Shop rodando (`http://localhost:3000`)

## O banco de dados por baixo do login

Quando você loga, o servidor monta uma consulta **SQL** pra perguntar ao banco se aquele usuário e senha existem. Simplificando, é algo assim:

```sql
SELECT * FROM usuarios WHERE email = 'SEU_INPUT' AND senha = '...'
```

O `SEU_INPUT` é o que você digitou no campo de e-mail. O problema: o servidor **cola o seu texto direto dentro do comando**. Se você digitar um e-mail normal, tudo bem. Mas e se você digitar aspas?

## Quebrando a consulta

Imagine que no campo de e-mail você digite isto:

```
' OR 1=1--
```

A consulta que o servidor monta vira:

```sql
SELECT * FROM usuarios WHERE email = '' OR 1=1--' AND senha = '...'
```

Olha o que aconteceu, pedaço por pedaço:

- A **aspa** `'` fecha o campo de e-mail mais cedo do que o servidor esperava.
- O `OR 1=1` adiciona uma condição que é **sempre verdadeira**.
- O `--` transforma o resto da linha em **comentário** — a checagem de senha some.

Resultado: a consulta retorna o primeiro usuário da tabela (normalmente o **admin**), e o servidor te loga como ele. Sem senha.

> 💡 `1=1` é sempre verdade, então `WHERE qualquer_coisa OR 1=1` casa com **todas** as linhas. Esse é o padrão que se repete em quase toda injeção de SQL: fechar a aspa, injetar uma lógica sua, comentar o resto.

## Do login ao banco inteiro

Bypass de login é só o começo. Com injeção dá pra **ler dados que não são seus** usando `UNION`, que cola o resultado de uma segunda consulta na primeira:

```
' UNION SELECT usuario, senha FROM usuarios--
```

Se der certo, a página que mostrava produtos passa a mostrar **usuários e senhas**. Lembra da Aula 4, quando falei que você aprenderia a dumpar a tabela de usuários pra quebrar os hashes? É exatamente isto: a injeção derrama o banco, e o hashcat faz o resto.

Quando a página não mostra o resultado direto, existe o **blind SQL injection** — você faz o banco responder "sim/não" (ou demorar alguns segundos) e reconstrói o dado bit a bit. É mais lento e fica pra Aula 7.

## A ferramenta: sqlmap

Fazer injeção na mão ensina o conceito. Na vida real, o **sqlmap** automatiza: ele detecta o ponto de injeção, descobre o banco e extrai tudo sozinho.

```bash
sqlmap -u "http://localhost:3000/rest/products/search?q=teste" --dbs
```

Aprenda na mão primeiro; use o sqlmap depois pra ir rápido.

## Desafio — Entre como admin sem a senha

O login do Juice Shop monta a consulta do jeito inseguro que você viu. Entre na conta de **administrador** sem saber a senha dele.

??? tip "💡 Dica"
    O campo de e-mail vira parte do `SELECT`. Feche a aspa, force uma condição verdadeira e comente o resto da linha.

??? success "✅ Solução"
    No campo de **e-mail** digite:

    ```
    ' OR 1=1--
    ```

    (repare no espaço depois do `--`). No campo de senha, qualquer coisa. Você entra como o primeiro usuário da tabela, o admin — isso resolve o desafio **Login Admin**.

    Se quiser entrar como o admin especificamente, `admin@juice-sh.op'--` também funciona: a aspa fecha o e-mail e o `--` corta a checagem de senha.

## Como se defende

A causa da injeção é **misturar dado com comando**. A defesa é separar os dois:

- **Prepared statements (consultas parametrizadas).** O código manda o comando e os dados por canais separados; o banco nunca interpreta o seu input como SQL.
- **ORM.** Bibliotecas como Sequelize/Django ORM já usam parametrização por baixo — desde que você não concatene string na mão.
- **Menor privilégio.** O usuário que a aplicação usa no banco não precisa poder apagar tabelas. Se ele não pode, uma injeção causa menos estrago.
- **Validação de input** como camada extra — nunca como a única.

## Próximos passos

Você fez o banco obedecer. A próxima página faz o **navegador** obedecer: **[Cross-Site Scripting (XSS)](xss.md)**.
