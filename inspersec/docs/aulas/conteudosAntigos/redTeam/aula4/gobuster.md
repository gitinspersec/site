# Gobuster

## O que é o Gobuster?

O **Gobuster** é uma ferramenta escrita em Go para brute-forcing de URIs (diretórios e arquivos) e subdomínios DNS em aplicações web. É amplamente usada em fases de _reconnaissance_ em CTFs e testes de penetração devido à sua velocidade e flexibilidade.

Principais características:
- Implementada em Go, oferece alta performance e baixo consumo de recursos.
- Suporte nativo a multithreading.
- Múltiplos modos de operação:  
  - `dir`     – força diretórios e arquivos HTTP  
  - `dns`     – enumera subdomínios DNS  
  - `vhost`  – força nomes de host virtuais  
  - `fuzz`    – fuzz de parâmetros HTTP  
- Permite customizar cabeçalhos HTTP, extensões, códigos de status a ignorar, etc.

---

## Instalação no Ubuntu

Existem três formas principais de instalar o Gobuster em Ubuntu:

### 1. Via APT (repositório oficial)

```bash
sudo apt update
sudo apt install gobuster
```

### 2. Via Snap

```bash
sudo snap install gobuster
```

## Sintaxe Básica

```bash
gobuster <modo> [opções]
```

* `<modo>`: `dir`, `dns`, `vhost`, `fuzz`
* As opções variam conforme o modo, mas algumas são comuns:

| Opção                | Descrição                                       |
| -------------------- | ----------------------------------------------- |
| `-u`, `--url`        | URL alvo (HTTP/HTTPS)                           |
| `-w`, `--wordlist`   | Caminho para a wordlist                         |
| `-t`, `--threads`    | Número de threads simultâneas (padrão: 10)      |
| `-x`, `--extensions` | Extensões de arquivo a append (ex: `php,html`)  |
| `-o`, `--output`     | Arquivo para salvar resultados                  |
| `-s`, `--status`     | Códigos de status HTTP a exibir (ex: `200,204`) |
| `-b`, `--blacklist`  | Status a ignorar (ex: `404,403`)                |

---

## Exemplos de Uso

### 1. Forçar diretórios e arquivos

```bash
gobuster dir \
  -u https://exemplo.com/ \
  -w /usr/share/wordlists/dirb/common.txt \
  -x php,html,txt \
  -t 50 \
  -o gobuster_dir.txt
```

* **Explicação**:

    * força diretórios/arquivos em `https://exemplo.com/`
    * usa wordlist `common.txt`
    * adiciona as extensões `.php`, `.html`, `.txt`
    * 50 threads simultâneas
    * grava em `gobuster_dir.txt`

### 2. Enumeração de subdomínios (modo DNS)

```bash
gobuster dns \
  -d exemplo.com \
  -w /usr/share/wordlists/dns/subdomains-top1million.txt \
  -t 20 \
  -o gobuster_dns.txt
```

- **Explicação**:

      - domínio alvo: `exemplo.com`
      - wordlist de subdomínios
      - 20 threads

### 3. Fuzz de parâmetros HTTP (modo fuzz)

```bash
gobuster fuzz \
  -u 'https://exemplo.com/page.php?FUZZ=test' \
  -w /usr/share/wordlists/raft-small-words.txt \
  -t 30 \
  -o gobuster_fuzz.txt
```

* **Explicação**:

    * substitui `FUZZ` pelo conteúdo da wordlist
    * permite descobrir parâmetros adicionais escondidos

---

## Dicas e Boas Práticas

1. **Seleção de wordlists**: use listas específicas para diretórios, arquivos, subdomínios e parâmetros.
2. **Threads**: ajuste o número de threads conforme a capacidade do alvo e sua conexão.
3. **Códigos de status**: filtre apenas os códigos de interesse (`200`, `301`, `302`), ignore `404`/`403`.
4. **Cabeçalhos customizados**: para aplicações que exigem autenticação ou headers especiais, use a opção `-H`.
5. **Saída e relatórios**: sempre grave resultados em arquivo e, ao final, consolide os achados.

---

> **Referências**a
>
> * Repositório oficial do Gobuster: [https://github.com/OJ/gobuster](https://github.com/OJ/gobuster)
> * Documentação de wordlists Kali: `/usr/share/wordlists`
