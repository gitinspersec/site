# Shells e Processos

*⏱️ Tempo de leitura: ~5 min*

> Material complementar da Aula 1. Duas coisas que todo mundo usa no terminal sem sempre saber o nome: o **shell** (quem lê os seus comandos) e os **processos** (os programas rodando).

## Pré-requisitos

- Ter lido [Linux Básico](../aula1/linux.md)

## O que é um shell

O **shell** é o programa que fica lendo o que você digita no terminal e mandando o sistema executar. Quando você roda `ls`, é o shell que interpreta a linha e chama o programa certo.

Terminal e shell não são a mesma coisa: o *terminal* é a janela; o *shell* é o programa que roda dentro dela. Veja qual é o seu:

```bash
echo $SHELL
```

Os mais comuns:

| Shell | Característica |
|-------|---------------|
| `bash` | o padrão na maioria das distros; o que você vai usar |
| `sh` | mínimo e universal; é o shell dos scripts de sistema |
| `zsh` | parecido com o bash, com mais recursos; padrão do Kali novo e do macOS |
| `fish` | focado em ser amigável: autocompleta e colore sozinho |

Pra trocar o seu shell padrão (vale a partir do próximo login):

```bash
chsh -s /bin/zsh
```
> 💡 Você precisa ter instalado o ZSH pro comando acima funcionar.

## Processos: o que está rodando

Todo programa em execução é um **processo**, e cada um tem um número, o **PID**. Um processo pode estar em **primeiro plano** (foreground — segura o terminal até terminar) ou em **segundo plano** (background — roda solto enquanto você usa o terminal pra outra coisa).

### Controlando o que está em primeiro plano

Quando um comando está rodando e travando o terminal:

- `Ctrl + C` — encerra o processo.
- `Ctrl + Z` — pausa e devolve o terminal (o processo fica parado em segundo plano).

### Mandando pro segundo plano

| Comando | O que faz |
|---------|-----------|
| `comando &` | já inicia o comando em segundo plano |
| `jobs` | lista o que está em segundo plano nesta janela |
| `fg` | traz de volta pro primeiro plano |
| `bg` | retoma em segundo plano um processo pausado (o do `Ctrl+Z`) |

### Vendo e matando processos

| Comando | O que faz |
|---------|-----------|
| `ps aux` | lista todos os processos do sistema |
| `top` / `htop` | o "gerenciador de tarefas" ao vivo (`htop` é mais bonito; `q` sai) |
| `kill PID` | pede pro processo encerrar |
| `kill -9 PID` | força o encerramento (quando o processo travou de vez) |

> 💡 O `top` já vem instalado. O `htop` (mais bonito) você instala com `sudo apt install htop`.

> 💡 Na Aula 3, o `docker run --rm -it ...` **trava o terminal** enquanto o alvo roda. É primeiro plano: dá pra parar com `Ctrl+C`, abrir um segundo terminal pra trabalhar, ou rodar com `&` pra deixar em segundo plano.

## Bônus: shell scripting

Um **script** é só uma sequência de comandos salva num arquivo `.sh`, pra rodar tudo de uma vez em vez de digitar linha por linha. Ele começa com uma linha especial, o *shebang*, que diz qual shell usar:

```bash
#!/bin/bash
echo "Olá!"
```

Pra rodar, você dá permissão de execução (lembra do `chmod`?) e chama o arquivo:

```bash
chmod +x script.sh
./script.sh
```

Dá pra ir muito além disso — variáveis, condições, laços. Fica pra uma aula futura.
