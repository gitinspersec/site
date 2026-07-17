# Linux Básico

*⏱️ Tempo de leitura: ~8 min*

> As ferramentas que você vai usar no curso rodam em Linux. Esta página cobre o suficiente pra você se virar no terminal.

## Pré-requisitos

- Ter lido [Fundamentos de Cibersegurança](fundamentos.md)
- Nenhuma experiência com terminal é necessária

## Por que Linux?

Quase toda ferramenta de pentest é feita pra Linux, e a maioria dos servidores que você vai atacar roda Linux. A distribuição que usamos é o **Kali**, que já vem com as ferramentas de segurança instaladas.

Não precisa decorar os comandos abaixo; o normal é consultar quando precisar.

## O terminal

O terminal é onde você digita comandos em vez de clicar. O texto antes do cursor é o *prompt*, que costuma mostrar seu usuário e a pasta atual:

```
usuario@kali:~$
```

O `~` é um atalho pra sua pasta pessoal (`/home/usuario`).

O `$` indica um usuário comum e vira `#` quando você é root (administrador).

## Navegação

| Comando | O que faz |
|---------|-----------|
| `pwd` | Mostra a pasta onde você está |
| `ls` | Lista arquivos e pastas |
| `ls -la` | Lista tudo, incluindo ocultos e permissões |
| `cd pasta` | Entra numa pasta |
| `cd ..` | Volta uma pasta |
| `cd` | Volta pra sua pasta pessoal |

**Caminho absoluto** começa na raiz `/` (ex.: `/etc/passwd`). 

**Caminho relativo** parte de onde você está (ex.: `Downloads/arquivo.txt`).

> 💡 Dica: Pressione Tab para autocompletar os comandos com os nomes de pastas e arquivos disponíveis.

## Arquivos e pastas

| Comando | O que faz |
|---------|-----------|
| `cat arquivo` | Mostra o conteúdo do arquivo |
| `less arquivo` | Leitura paginada (`q` pra sair) |
| `mkdir pasta` | Cria uma pasta |
| `touch arquivo` | Cria um arquivo vazio |
| `cp origem destino` | Copia |
| `mv origem destino` | Move ou renomeia |
| `rm arquivo` | Apaga um arquivo |
| `rm -r pasta` | Apaga uma pasta e tudo dentro dela |

> ⚠️ `rm` não tem lixeira. Arquivo apagado não volta.

## Ler e filtrar conteúdo

Esses comandos aparecem o tempo todo em desafios de segurança:

| Comando | O que faz |
|---------|-----------|
| `grep texto arquivo` | Mostra só as linhas que contêm um texto |
| `find . -name "*.txt"` | Procura arquivos por nome a partir da pasta atual |
| `file arquivo` | Diz que tipo de arquivo é |
| `wc -l arquivo` | Conta as linhas |
| `sort` / `uniq` | Ordena / remove linhas repetidas |

## Juntando comandos: pipes e redirecionamento

O `|` (pipe) manda a saída de um comando pra entrada de outro:

```bash
cat lista.txt | grep senha
```

O `>` salva a saída num arquivo (sobrescreve) e o `>>` adiciona no final:

```bash
ls -la > saida.txt
```

## Permissões e superusuário

Cada arquivo tem permissões de leitura (`r`), escrita (`w`) e execução (`x`), que você vê com `ls -l`:

```
-rwxr-xr-- 1 user user 120 script.sh
```

### A anatomia do `-rwxr-xr--`

Esqueça o primeiro caractere por um instante — ele só diz o **tipo** (`-` pra arquivo, `d` pra diretório). Sobram 9 caracteres, divididos em **3 blocos de 3**:

```
-  rwx  r-x  r--
│  │    │    │
│  │    │    └─ Outros (resto do mundo)
│  │    └─ Grupo (seus colegas)
│  └─ Dono (você)
└─ Tipo (- arquivo, d diretório)
```

Dentro de cada bloco a ordem é sempre a mesma: `r` (read), `w` (write), `x` (execute). Um hífen `-` no lugar de uma letra significa "não pode".

No exemplo `-rwxr-xr--`:

- **Dono — `rwx`**: você, que criou o arquivo. Pode ler, escrever e executar. Controle total.
- **Grupo — `r-x`**: imagine que você e seus colegas de projeto estão num grupo chamado "infosec". Quem está no grupo pode ler e executar, mas o `-` no meio mostra que **não** pode escrever (alterar o arquivo).
- **Outros — `r--`**: qualquer outro usuário do sistema. Só consegue ler. Não altera e não executa.

### Mudando permissões e virando root

- `chmod +x arquivo` — dá permissão de execução (`x`)
- `sudo comando` — roda o comando como **root**, o administrador que pode tudo

Tarefas que mexem no sistema (instalar programas, editar arquivos protegidos) exigem `sudo`.

## Instalar programas

No Kali e no Ubuntu, o gerenciador de pacotes é o `apt`:

```bash
sudo apt update            # atualiza a lista de pacotes
sudo apt install nmap      # instala um programa
```

## Editar arquivos: nano

Pra quem está começando, o `nano` é o editor mais simples:

```bash
nano arquivo.txt
```

Você edita direto na tela. `Ctrl+O` salva e `Ctrl+X` sai. (Existe também o `vim`, mais poderoso e mais difícil — fica pra depois.)

## Para casa

São **duas tarefas separadas**. Pode fazer na ordem que quiser — uma não depende da outra.

### Tarefa 1 — Instale o Linux

Instale o **WSL + Kali** no seu computador. É o caminho mais tranquilo no Windows, e você vai precisar dele nas próximas aulas.

➡️ [Instalando o Kali no WSL](../../../Instalação/kaliWindows.md)

Depois de instalar, abra o terminal e teste os comandos desta página.

### Tarefa 2 — Comece o Bandit

O **Bandit** é um jogo de níveis que treina exatamente esses comandos resolvendo desafios reais. Cada nível te dá a senha do próximo.

> 💡 O Bandit roda num servidor remoto e você se conecta por **SSH** — dá pra fazer **direto do PowerShell do Windows**, sem ter o Linux instalado. Por isso a Tarefa 2 **não depende** da Tarefa 1.

Conecte no primeiro nível assim (a senha do nível 0 é `bandit0`):

```
ssh bandit0@bandit.labs.overthewire.org -p 2220
```
As instruções de cada nível estão em [overthewire.org/wargames/bandit](https://overthewire.org/wargames/bandit/).

O ideal é chegar até o **nível 20**. Quando travar num nível, pesquisar a solução é normal e ajuda a aprender, mas garanta que você entende os comandos que está executando e o por quê de cada um deles. 

