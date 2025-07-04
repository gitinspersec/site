# John the Ripper

## O que é o John the Ripper?

O **John the Ripper** (ou simplesmente **John**) é uma das ferramentas de cracking de senhas mais conhecidas e poderosas no mundo de segurança da informação. Desenvolvido para testes de penetração e auditorias de segurança, ele tenta “quebrar” hashes de senhas usando diversos modos de ataque:

- **Dictionary attack** (ataque por dicionário)
- **Brute-force / incremental**  
- **Single mode** (usa informações do sistema para gerar mutações mais inteligentes)
- **Rule-based** (aplica regras de transformação a uma wordlist)

John é altamente configurável, suporta dezenas de formatos de hash (MD5, SHA-*s*, NTLM, bcrypt, etc.) e, na sua versão “Jumbo”, conta até com patches para GPUs e técnicas avançadas.

---

## Instalação no Ubuntu

### 1. Versão padrão (repositório APT)

```bash
sudo apt update
sudo apt install john
```

### 2. Versão “Jumbo” compilada do código-fonte

A versão Jumbo oferece suporte a mais formatos e otimizações (incluindo CUDA/OpenCL).

1. Instale dependências:

   ```bash
   sudo apt update
   sudo apt install build-essential libssl-dev yasm git
   ```
2. Clone o repositório e compile:

   ```bash
   git clone https://github.com/openwall/john -b bleeding-jumbo john-jumbo
   cd john-jumbo/src
   ./configure && make -s clean && make -sj4
   ```
3. Execute o binário compilado:

   ```bash
   cd ../run
   ./john --test
   ```

---

## Estrutura de arquivos

* `john` — binário principal
* `john.conf` — arquivo de configuração (regras, formatos, sessões)
* `john.pot` — arquivo de pot (“potfile”), onde são gravadas as senhas já quebradas
* `run/` — diretório com scripts auxiliares e wordlists básicas

---

## Sintaxe Básica

```bash
john [OPÇÕES] <arquivo_de_hashes>
```

Principais opções:

| Opção                  | Descrição                                                   |
| ---------------------- | ----------------------------------------------------------- |
| `--wordlist=<arquivo>` | Usa dicionário (modo wordlist)                              |
| `--rules[=nome]`       | Aplica regras de mutação ao dicionário (requer `–wordlist`) |
| `--incremental[=modo]` | Modo incremental (força todas as combinações)               |
| `--format=<formato>`   | Força uso de determinado formato de hash                    |
| `--show`               | Exibe senhas já quebradas e o progresso                     |
| `--session=<nome>`     | Salva/retoma uma sessão de cracking                         |
| `--pot=<arquivo>`      | Especifica outro potfile                                    |
| `--stdout`             | Gera mutações do dicionário na saída padrão (sem crackear)  |
| `--mask=<máscara>`     | Ataque por máscara (ex.: `?l?l?l?l?d?d`)                    |

---

## Modos de Ataque e Exemplos

### 1. Dictionary Attack (*Wordlist*)

```bash
john --wordlist=/usr/share/wordlists/dirb/common.txt \
     --rules \
     hashes.txt
```

* Carrega as senhas-hash de `hashes.txt`
* Usa `common.txt` + as regras definidas em `john.conf` para gerar mutações (ex.: adicionar números, trocar letras maiúsculas, etc.)

### 2. Single Mode

```bash
john --single hashes.txt
```

* Explora nomes de usuários, GECOS e variações de informações do próprio sistema para gerar candidatos de senha

### 3. Incremental Mode

```bash
john --incremental hashes.txt
```

* Modo “força bruta” incremental: tenta todas as combinações de caracteres definidas em `john.conf`
* Mais lento, mas garante cobertura completa até um certo comprimento

### 4. Ataque por Máscara

```bash
john --mask='?u?l?l?l?d?d' hashes.txt
```

* `?u` = letra maiúscula; `?l` = letra minúscula; `?d` = dígito
* Útil para senhas com formato conhecido (ex.: 1 Maiúscula + 3 minúsculas + 2 dígitos)

### 5. Mostrar Resultados

Depois de rodar qualquer modo:

```bash
john --show hashes.txt
```

* Exibe quais hashes já foram quebrados e as senhas correspondentes

---

## Boas Práticas e Dicas

1. **Comece sempre por wordlists + regras** — geralmente é mais rápido e eficiente.
2. **Use sessões**: `--session=minha_sessao` para pausar e retomar sem perder progresso.
3. **Escolha o modo incremental com cuidado** — consome muito tempo. Ajuste os caracteres em `john.conf` se quiser acelerar.
4. **Combine técnicas**: após o término do dictionary + rules, faça um ataque incremental ou por máscara.
5. **Monitore o uso de GPU** (na versão Jumbo com OpenCL/CUDA) para maximizar performance.
6. **Proteja seu potfile**: se estiver em ambiente compartilhado, use `--pot=arquivo_secreto.pot`.

---

> **Referências**
>
> * Projeto John the Ripper (OpenWall): [https://www.openwall.com/john/](https://www.openwall.com/john/)
> * Repositório Jumbo: [https://github.com/openwall/john/tree/bleeding-jumbo](https://github.com/openwall/john/tree/bleeding-jumbo)
> * Documentação de máscaras e modos: `doc/MODES` no código-fonte

