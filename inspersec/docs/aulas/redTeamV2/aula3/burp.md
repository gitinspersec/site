# Interceptação com Burp Suite

*⏱️ Tempo de leitura: ~5 min*

> O navegador manda requisições pro servidor o tempo todo. O Burp te deixa ver e alterar cada uma antes de ela chegar lá.

## Pré-requisitos

- Ter o Juice Shop rodando (ver [página anterior](reconhecimento.md))
- Burp Suite Community no Windows (instalação abaixo)

## Instale o Burp (no Windows)

O Burp é uma ferramenta **gráfica** — não roda no terminal do WSL. Instale a versão **Community** (grátis) no próprio Windows:

1. Baixe em [portswigger.net/burp/communitydownload](https://portswigger.net/burp/communitydownload).
2. Instale, abra e escolha *Temporary project* → *Use Burp defaults* → *Start Burp*.

Como o Juice Shop roda no Docker (dentro do WSL) e o WSL espelha o `localhost` pro Windows, o Burp no Windows alcança ele em `http://localhost:3000` normalmente.

## O que é o Burp

O Burp é um **proxy de interceptação**: ele fica no meio do caminho entre o navegador e o servidor. Toda requisição passa por ele antes de seguir, e você pode ler, modificar ou repetir.

É o mesmo Man-in-the-Middle do extra de HTTPS — só que a seu favor, no seu próprio tráfego.

```
Navegador  ──>  Burp (você)  ──>  Servidor
```

## Configurando (o jeito fácil)

Use o **navegador embutido** do Burp, que já vem apontado pra ele:

1. Aba **Proxy** → botão **Open Browser** (abre um Chromium já configurado).
2. Nesse navegador, acesse `http://localhost:3000`.

## Proxy: interceptar e alterar

Com o **Intercept is on** (aba Proxy), faça uma ação no Juice Shop. A requisição **para no Burp**, esperando você. Aí você pode:

- **Forward** — deixa seguir como está.
- **Editar antes** — muda um valor no corpo ou nos headers e dá Forward.
- **Drop** — descarta a requisição.

## Repeater e Intruder

- **Repeater** — mande uma requisição pra ele (botão direito → *Send to Repeater*) e reenvie quantas vezes quiser, mudando o que quiser a cada tentativa. É o "e se eu trocar esse valor?" sem ter que repetir a ação no site.
- **Intruder** — automatiza: repete a requisição trocando um pedaço marcado por uma lista de valores.
    1. Em **Positions**, marque o que vai variar (ex.: um `id`).
    2. Em **Payloads**, ponha a lista de valores a testar.
    3. **Start attack** e compare as respostas (status e tamanho).

> ⚠️ Na versão Community o Intruder é **lento de propósito** (throttling). Serve pra aprender e pra listas pequenas.

## Desafios

Objetivo, não receita. Tente antes de abrir as dicas.

### Desafio — Faça a loja te pagar

Dá pra fazer um pedido cujo total fica **negativo** — em vez de você pagar, a loja fica te devendo. Usando o Burp pra mexer no tráfego, consiga isso.

??? tip "💡 Dica"
    O navegador manda a quantidade de cada item pro servidor. E se você interceptar e enviar uma quantidade que o site nunca deixaria você digitar no botão?

??? success "✅ Solução"
    Adicione um produto ao carrinho e **intercepte** a requisição no Burp (ou mande ela pro **Repeater**). No corpo, troque a quantidade por um número **negativo** e dê Forward / Send. O total do pedido fica negativo — isso resolve o desafio **Payback Time** ("faça um pedido que te deixa rico").

## Próximos passos

Você já intercepta e altera requisições. A última página da aula é **[Força bruta](forca-bruta.md)**: descobrir senhas, online e offline.
