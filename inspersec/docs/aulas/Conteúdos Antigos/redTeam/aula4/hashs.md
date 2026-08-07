# Hashes e CTFs

## Objetivos da Aula

* **Entender** o que é uma função hash e suas principais propriedades.
* **Conhecer** os algoritmos de hash mais usados.
* **Explorar** por que hashes aparecem com frequência em desafios de CTF.
* **Praticar** geração e quebra de hashes em cenários simples.

## 1. O que é um Hash?

Um **hash** é o resultado de uma função matemática que mapeia dados de tamanho arbitrário (texto, arquivo, senha) para um valor de tamanho fixo (cadeia de bytes ou hexadecimal).

Exemplo:

```bash

# MD5 de "senha123"
echo -n "senha123" | md5sum
# saída: 482c811da5d5b4bc6d497ffa98491e38
```

### 1.1 Função Hash vs. Hash Criptográfico

* **Hash genérico** (ex.: CRC) detecta erros de transmissão.
* **Hash criptográfico** (ex.: MD5, SHA-256, bcrypt) oferece segurança contra inversão e colisões.

## 2. Propriedades de uma Função Hash Criptográfica

1. **Determinística**: mesma entrada gera sempre o mesmo hash.
2. **Rápida de computar**: eficiente para entradas grandes.
3. **Resistência a colisões**: difícil encontrar duas entradas diferentes com o mesmo hash.
4. **Resistência a pré-imagem**: dado o hash, difícil descobrir a entrada original.
5. **Avalanche Effect**: pequena mudança na entrada altera drasticamente o hash.

## 3. Algoritmos de Hash Comuns

| Algoritmo | Tamanho (bits) | Uso Típico                      |
| --------- | -------------- | ------------------------------- |
| MD5       | 128            | Verificação geral (não seguro)  |
| SHA-1     | 160            | Histórico (desaconselhado hoje) |
| SHA-256   | 256            | Assinaturas digitais, SSL/TLS   |
| bcrypt    | variável       | Armazenamento seguro de senhas  |
| Argon2    | variável       | Password hashing moderno        |

## 4. Exemplos de Uso do Hash no Dia a Dia

* **Armazenamento de senhas**: servidores guardam só o hash.
* **Verificação de integridade**: confirma download correto de arquivos.
* **Blockchain**: encadeamento de blocos via hashes.

## 5. Importância em CTFs

1. **Desafios de Cracking**: fornecem hashes de flags ou senhas para quebrar com ferramentas.
2. **Forense Digital**: validação de evidências e integridade forense.
3. **Verificação de Arquivos**: organizadores exibem hash oficial para checar downloads.
4. **Protocolos e Autenticação**: implementação de mecanismos seguros em desafios.

## 6. Demonstração Prática

```python
import hashlib

texto = "CTF{exemplo_de_flag}"
print("MD5:  ", hashlib.md5(texto.encode()).hexdigest())
print("SHA256:", hashlib.sha256(texto.encode()).hexdigest())
```

> **Exercício**: modifique para ler `arquivo.txt` e imprimir seus hashes MD5 e SHA-256.

## 7. Ferramentas de Cracking

* **John the Ripper**: suporte a vários formatos e regras.
* **hashcat**: aceleração via GPU.
* **hash-identifier**: identifica tipo de hash.

## 8. Exercícios Propostos

1. Gere hashes MD5 e SHA-1 de diferentes senhas.
2. Quebre um hash MD5 de até 6 caracteres com John the Ripper.
3. Compartilhe um arquivo com hash SHA-256 para verificação de colegas.
4. Crie um mini-CTF: gere o hash de uma flag e desafie um amigo.

## 9. Recapitulação

* **Hash**: valor fixo de dados de qualquer tamanho.
* **Propriedades criptográficas**: segurança contra inversão e colisões.
* **CTFs**: hash em cracking, integridade e forense.
