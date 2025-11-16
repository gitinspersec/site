# John The Ripper: Quebrador de senhas

## O que é o John The Ripper
John The Ripper é uma ferramenta de código aberto desenvolvida para realizar o cracking de senhas, ou seja, quebrar hashes de senhas para identificar combinações fracas ou comprometidas. Originalmente criado para sistemas Unix, atualmente suporta diversos formatos e plataformas, incluindo Windows, Linux, macOS e dispositivos móveis. 

## Como baixar e utilizar no Debian

### Passos para instalar no Debian:
1. Atualize os repositórios do sistema:
   ```bash
   sudo apt update && sudo apt upgrade
   ```
2. Instale o John The Ripper:
   ```bash
   sudo apt install john
   ```
3. Verifique a instalação:
   ```bash
   john --version
   ```

## Como o John The Ripper crackeia senhas
O John The Ripper utiliza técnicas como:

- **Força bruta**: testa todas as combinações possíveis.
- **Ataque por dicionário**: compara hashes com uma lista de senhas conhecidas.
- **Ataque híbrido**: combina palavras do dicionário com mutações (ex.: adicionar números ou caracteres especiais).

Essas abordagens são aplicadas conforme o tipo de hash e a configuração do sistema.

## Tipos de Hash (e suas vantagens/desvantagens)

| Algoritmo | Tamanho do hash | Vantagens | Desvantagens |
|-----------|-----------------|-----------|--------------|
| MD5       | 128 bits        | Rápido    | Vulnerável a colisões |
| SHA-1     | 160 bits        | Amplamente utilizado | Vulnerável a colisões |
| SHA-256   | 256 bits        | Mais seguro que MD5 e SHA-1 | Mais lento para calcular |

O SHA-256 é recomendado para aplicações que exigem maior segurança.

## Uso de Wordlists (ex.: RockYou)
Uma wordlist é uma lista de palavras utilizadas em ataques por dicionário. A wordlist RockYou, com milhões de senhas comuns, é frequentemente utilizada com o John The Ripper.

### Exemplo 1: Quebrando hash MD5 com RockYou

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt --format=raw-md5 hashes.txt
```
* O John vai aplicar a função MD5 em cada senha da wordlist RockYou e comparar com os hashes do arquivo. Se encontrar uma correspondência, a senha será revelada.

### Exemplo 2: Quebrando hash SHA-1 com RockYou

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt --format=raw-sha1 hashes.txt
```
* Igual ao exemplo anterior, mas agora utilizando SHA-1 como algoritmo de hash.

### Exemplo 3: Quebrando hash SHA-256 com RockYou

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt --format=raw-sha256 sha256.hash
```
* Utiliza o algoritmo SHA-256 para verificar se alguma senha da wordlist gera o hash desejado. Esse algoritmo é mais seguro que MD5 e SHA-1, mas exige mais processamento.

### Exemplo 4: Quebrando senha de arquivo ZIP (com hash extraído previamente)

```bash
zip2john arquivo.zip > zip.hash
john --wordlist=/usr/share/wordlists/rockyou.txt zip.hash
```
* Aqui, primeiro usamos `zip2john` para extrair o hash de um arquivo ZIP protegido por senha. Em seguida, usamos o John com a wordlist para tentar descobrir a senha do ZIP.

### Exemplo 5: Quebrando senha de hash bcrypt (mais lento)

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt --format=bcrypt bcrypt.hash
```
* Bcrypt é um algoritmo mais seguro e lento de propósito. Esse tipo de cracking pode demorar bastante, mas o John ainda consegue testar combinações da wordlist.


## Exemplos de Uso

### Exemplo de uso: Quebrando um hash MD5

1. Crie um arquivo com o hash MD5:
   ```bash
   echo "006f87892f47ef9aa60fa5ed01a440fb" > hashes.txt
   ```
2. Execute o John The Ripper:
   ```bash
   john --wordlist=rockyou.txt hashes.txt
   ```
3. Verifique a senha encontrada:
   ```bash
   john --show hashes.txt
   ```

Esse processo utiliza a wordlist para tentar encontrar a senha correspondente ao hash MD5 fornecido.

### Exemplo de uso: Quebrando um JWT simples

1. Obtenha o JWT e extraia a parte do cabeçalho e payload:
   ```bash
   JWT="eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ"
   HEADER_PAYLOAD=$(echo $JWT | cut -d '.' -f 1,2)
   ```

2. Crie um arquivo com o formato necessário para o John The Ripper:
   ```bash
   echo "$HEADER_PAYLOAD" > jwt.txt
   ```

3. Execute o John The Ripper com a wordlist:
   ```bash
   john --wordlist=rockyou.txt --format=raw-hmac-sha256 jwt.txt
   ```

4. Verifique a chave encontrada:
   ```bash
   john --show jwt.txt
   ```

Este exemplo utiliza a técnica de ataque HMAC-SHA256, onde o John The Ripper tenta diferentes chaves para verificar a assinatura do JWT.