# Força bruta: online e offline

*⏱️ Tempo de leitura: ~7 min*

> Uma senha cai de dois jeitos: tentando ao vivo no login (**online**) ou quebrando o hash roubado na sua máquina (**offline**). Aqui você faz os dois.

## Pré-requisitos

- Ter feito as páginas anteriores desta aula
- `nmap`, `hydra` e `hashcat` no Kali, com a `rockyou` pronta (setup no dever da Aula 2)

## O alvo: DVWA

Desta vez o alvo é o **DVWA** (Damn Vulnerable Web Application), outro app feito pra treinar. Suba com Docker:

```bash
docker run --rm -it -p 80:80 vulnerables/web-dvwa
```

Acesse `http://localhost`, entre com `admin` / `password`, clique em **Create / Reset Database** e, na aba **DVWA Security**, deixe em **Low**.

## Recon primeiro (Nmap)

Antes de atacar, veja o que a caixa expõe:

```bash
nmap -sV localhost
```

A porta **80** aparece com um servidor web — é onde mora o login. Num alvo real o Nmap acha várias portas; aqui o ponto é o hábito: **escaneie antes de bater na porta**.

## Dois caminhos pra mesma senha

| | Online (Hydra) | Offline (Hashcat) |
|--|----------------|-------------------|
| Contra o quê | o login ao vivo | um hash que você roubou |
| Velocidade | lento (rede + servidor) | rápido (sua máquina) |
| Barulho | alto (o servidor loga tudo) | nenhum |

Se dá pra pegar o hash e quebrar offline, é quase sempre o melhor caminho.

## Hashcat: quebrar o hash (offline)

O DVWA guarda as senhas como **MD5 sem salt** (lembra da Aula 2 por que isso é um problema?). Digamos que você dumpou a tabela de usuários — você aprende a fazer isso na Aula 6 — e saiu com estes hashes:

```
admin:5f4dcc3b5aa765d61d8327deb882cf99
gordonb:e99a18c428cb38d5f260853678922e03
pablo:0d107d09f5bbe40cade3de5c71e9e9b7
```

Salve as três linhas num `hashes.txt` e rode (`-m 0` = MD5; `--username` ignora o nome antes dos `:`):

```bash
hashcat -m 0 --username hashes.txt /usr/share/wordlists/rockyou.txt
```

## Hydra: adivinhar ao vivo (online)

Sem o hash, dá pra martelar o login direto. O DVWA tem uma página de treino de brute force em `/vulnerabilities/brute/` — um login vulnerável de mentirinha, feito pra você atacar.

Só que essa página fica **dentro** do DVWA: pra chegar nela, você precisa já estar logado no DVWA. É por isso que o Hydra manda junto o seu **cookie de sessão** (`PHPSESSID`) — ele é a prova de que você é o admin logado (o mesmo cookie de sessão da Aula 2). O cookie serve só pra *alcançar* a sala de treino; num alvo real o login é público e você não precisaria disso.

Pegue o `PHPSESSID` da sua sessão **logada** (F12 → aba *Application* → *Cookies*) e rode:

```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt localhost \
  http-get-form "/vulnerabilities/brute/:username=^USER^&password=^PASS^&Login=Login:H=Cookie: PHPSESSID=SEU_ID; security=low:F=Username and/or password incorrect."
```

O Hydra testa a rockyou contra o login e acha `admin : password` — a mesma senha que o hashcat quebrou, só que **ao vivo, sem ter o hash**. Repare que é mais lento que o offline, e o servidor registra cada tentativa (nada discreto).

> 💡 Se em algum momento aparecer *"CSRF token is incorrect"*, é uma defesa do DVWA: um código que muda a cada requisição pra impedir requisições forjadas. Garanta que sua sessão está **logada** e em **Low** que o Hydra funciona.

### Desafio — Recupere as senhas

Você dumpou os três hashes acima do DVWA. Quebre-os e recupere as senhas — inclusive as que você jamais adivinharia.

??? tip "💡 Dica"
    Cada hash tem 32 caracteres hex: é MD5. Jogue o arquivo no hashcat com a rockyou (`-m 0 --username`).

??? success "✅ Solução"
    O hashcat quebra os três: **password** (admin), **abc123** (gordonb) e **letmein** (pablo). Teste `admin` / `password` no módulo Brute Force (`/vulnerabilities/brute/`) — aparece *"Welcome to the password protected area admin"*. Você entrou como admin com a senha que **quebrou**, não adivinhou. E ainda saiu com contas que jamais chutaria: é o preço de guardar hash sem salt — um dump de banco vira acesso total.

## Fim da Aula 3

Você mapeou (Nmap + Gobuster), interceptou (Burp) e quebrou credenciais de dois jeitos — **offline** com Hashcat e **ao vivo** com Hydra.