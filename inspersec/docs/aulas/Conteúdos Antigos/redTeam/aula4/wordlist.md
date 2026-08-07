# Wordlists

## O que é uma Wordlist?

Uma **wordlist** é um arquivo de texto que contém inúmeras palavras, frases ou padrões usados para “adivinhar” valores em testes de segurança, tais como:

- **Brute-forcing de senhas**  
- **Fuzzing de diretórios/arquivos**  
- **Enumeração de subdomínios**  

Cada linha da wordlist será testada sequencialmente pela ferramenta contra o alvo.

---

## Exemplos de Wordlists Comuns

- **RockYou:**  
    - Contém milhões de senhas reais vazadas em 2009, muito usada em cracking de senhas.  
    - Download (compactado): [rockyou.txt.gz](https://github.com/danielmiessler/SecLists/raw/master/Passwords/Leaked-Databases/rockyou.txt.tar.gz  )
    

- **common.txt**  

    - Wordlist do Dirb com ~4.600 entradas de diretórios e arquivos padrões.  
    - Repositório (raw):  [common.txt](https://github.com/v0re/dirb/raw/master/wordlists/common.txt)

---

## Gerando Wordlists com **Crunch**

### O que é o Crunch?

O **Crunch** é uma ferramenta que gera wordlists “on the fly” combinando caracteres de acordo com parâmetros de comprimento e charset definidos pelo usuário. É ideal quando:
- Você conhece o formato aproximado da senha (ex.: 1 letra maiúscula + 3 letras minúsculas + 2 dígitos).  
- Quer criar listas personalizadas menores e mais direcionadas que as predefinidas.

### Instalação no Ubuntu

```bash
sudo apt update
sudo apt install crunch
```

### Sintaxe Básica

```
crunch <min> <max> [charset] [opções]
```

* `<min>`: comprimento mínimo das palavras geradas
* `<max>`: comprimento máximo
* `[charset]`: conjunto de caracteres a usar (ex.: `abcdef`, `0123456789`, `!@#`, etc.)

  * Para conjuntos pré-definidos, use:

    * `-f /usr/share/crunch/charset.lst mixalpha-numeric-all-space`
    * Veja todos em `/usr/share/crunch/charset.lst`

### Parâmetros Úteis

| Opção             | Descrição                                                      |
| ----------------- | -------------------------------------------------------------- |
| `-o <arquivo>`    | Grava a saída em arquivo                                       |
| `-t <máscara>`    | Gera apenas combinações que batem com a máscara (ex.: `@@@%%`) |
| `-b <tamanho>`    | Divide a wordlist em arquivos de até `<tamanho>` bytes         |
| `-c <número>`     | Mostra progresso a cada `<número>` de palavras geradas         |
| `-z <compressão>` | Comprime a saída (ex.: `gzip`, `bzip2`)                        |

### Máscaras (`-t`)

A máscara permite fixar posições ou tipos de caracteres:

* `@` → letra minúscula
* `,` → letra maiúscula
* `%` → dígito
* `^` → caracteres especiais

Ex.: `-t '@@%%'` gera todas as strings com 2 minúsculas seguidas de 2 dígitos.

### Exemplos

1. **Wordlist simples de 6 a 8 caracteres**, usando apenas letras minúsculas:

   ```bash
   crunch 6 8 abcdefghijklmnopqrstuvwxyz -o alpha.txt
   ```

2. **Combinando minúsculas, maiúsculas e dígitos** (tamanho exato 6):

   ```bash
   crunch 6 6 abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789 -o alnum6.txt
   ```

3. **Usando máscara para “1 Maiúscula + 4 minúsculas + 2 dígitos”**:

   ```bash
   crunch 7 7 -t ',@@@@%%' -o mask.txt
   ```

   * `,` → 1 letra maiúscula
   * `@@@@` → 4 letras minúsculas
   * `%%` → 2 dígitos

4. **Dividindo em arquivos de 100 MB**:

   ```bash
   crunch 6 8 abcdef123 -b 100M -o crunch_part
   ```

   Isso gerará `crunch_part00`, `crunch_part01`, etc.

5. **Comprimindo a saída com gzip**:

   ```bash
   crunch 6 6 abcdef123 -z gzip -o crunch.gz
   ```

---

> **Dica**: use `-c 10000` para receber feedback de progresso a cada 10 000 linhas geradas, evitando bloqueios silenciosos em listas muito grandes.

