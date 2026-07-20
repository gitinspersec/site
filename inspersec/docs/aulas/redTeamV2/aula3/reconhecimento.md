# Reconhecimento e Enumeração

*⏱️ Tempo de leitura: ~4 min*

> Primeira aula-laboratório. Aqui você para de ler sobre ataque e começa a atacar — num alvo controlado.

## Pré-requisitos

- Ter feito as Aulas 1 e 2 e lido os extras de [HTTP e HTTPS](../extras/http_https.md) e [Protocolos, IP e Portas](../extras/protocolos_tcp.md)
- Um Linux com Docker instalado (o Kali já vem com as ferramentas de pentest, mas qualquer um serve)

## Como se pensa um ataque

Um pentest não é sair chutando exploit. Segue uma ordem:

1. **Reconhecimento** — descobrir o que o alvo expõe (portas, serviços, páginas).
2. **Enumeração** — cavar cada coisa encontrada (diretórios, parâmetros, usuários).
3. **Interceptação** — ver e alterar o tráfego (próxima página).
4. **Exploração** — abusar do que achou.

As páginas de laboratório seguem essa ordem. Hoje é o começo: achar a superfície de ataque.

## O alvo: OWASP Juice Shop

Você vai atacar o **OWASP Juice Shop**, uma loja online *propositalmente cheia de falhas*. Suba ela com Docker:

```bash
docker run -d -p 3000:3000 bkimminich/juice-shop
```

Acesse `http://localhost:3000` no navegador.

Não tem Docker? Instale com um comando (é o script oficial):

```bash
curl -fsSL https://get.docker.com | sh
```

No WSL, ligue o serviço depois com `sudo service docker start` (e use `sudo` no `docker run` se pedir permissão).

## A ferramenta: Gobuster

Um site tem páginas que não aparecem nos links visíveis: painéis, backups, arquivos esquecidos. O **Gobuster** acha isso testando nomes de uma wordlist, um atrás do outro.

```bash
gobuster dir -u http://localhost:3000 -w /usr/share/wordlists/seclists/Discovery/Web-Content/common.txt
```

- `dir` — modo de busca por diretórios e arquivos
- `-u` — a URL do alvo
- `-w` — a wordlist (a lista de nomes a testar)

> ⚠️ Se a `seclists` não estiver instalada, rode `sudo apt install seclists`.

Cada achado vem com o status HTTP (lembra do extra de HTTP?): `200` existe, `301/302` redireciona, `403` é "existe, mas você não pode ver".

## Desafios

Aqui vai o **objetivo**, não a receita. Tente sozinho antes de abrir as dicas — o valor está em descobrir.

### Desafio 1 — Ache o que não devia estar público

O Juice Shop tem um diretório com arquivos internos que nunca deveriam estar acessíveis de fora. Enumere o site e encontre ele.

??? tip "💡 Dica"
    Se ele reclamar que "o servidor responde igual pra URLs que não existem", use `--exclude-length` com o número que ele mostrar.

??? success "✅ Solução"
    O Gobuster encontra `/ftp` (Status `200`). Abra `http://localhost:3000/ftp` no navegador e veja os arquivos internos listados.

### Extra — Baixe o que o servidor não deixa

Nesse diretório que você achou tem arquivos (`.bak`, `.kdbx`) que o servidor recusa entregar — ele só libera `.md` e `.pdf`. Baixe um deles mesmo assim.

??? tip "💡 Dica"
    Pesquise **poison null byte**.

??? success "✅ Solução"
    Um *poison null byte* (`%2500`) engana a validação:

    ```
    http://localhost:3000/ftp/coupons_2013.md.bak%2500.md
    ```

    O servidor vê o `.md` no fim e libera; o sistema de arquivos para no null byte e entrega o `.bak`.

    **Por que `%2500` e não `%00`?** O null byte é `%00`. Aqui ele vai *duplo-encodado*: `%25` é o próprio `%`, então `%2500` só vira `%00` **depois** de uma camada de decode. Alguma camada decodifica a URL uma vez antes de checar a extensão — um `%00` puro seria consumido cedo demais. É a mesma ideia de camadas de encoding da Aula 2: quando uma some no caminho, você põe outra por cima.

    Isso resolve o **Forgotten Sales Backup** (⭐⭐⭐⭐); o mesmo truque baixa o `package.json.bak` (**Forgotten Developer Backup**).

## Próximos passos

Você já mapeou o alvo. A próxima página é o **[Burp Suite](burp.md)**: interceptar e alterar o que o navegador manda pro servidor.
