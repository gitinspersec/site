
### Material Necessário

Para os laboratórios práticos, utilizaremos o **Kali Linux**, uma das distribuições mais completas para pentest e segurança. Sabemos que muitos não desejam instalar o Kali em uma máquina virtual ou fazer dual boot, então aqui está uma alternativa que chamamos de **Kali Windows**, que permite rodar o Kali diretamente no **Windows 10** usando o WSL (Windows Subsystem for Linux).


Caso prefira instalar o Kali Linux em uma máquina virtual ou fazer dual boot, entre em contato com a gestão para obter ajuda, ou vá até a sala 404 do Prédio 1 e procure o Rogério para um suporte mais detalhado.

## Instalação do Kali Linux no Windows

### Passo 1: Habilitar o WSL

Primeiro, habilite o **Windows Subsystem for Linux (WSL)** no seu Windows. Você pode baixar o WSL diretamente da Microsoft Store clicando no link abaixo:

- [Baixar WSL](https://apps.microsoft.com/store/detail/windows-subsystem-for-linux/9P9TQF7MRM4R)

### Passo 2: Instalar o Kali Linux

Após instalar o WSL, você pode proceder com a instalação do **Kali Linux**. Siga o link abaixo para a instalação:

- [Baixar Kali Linux](https://apps.microsoft.com/detail/9pkr34tncv07?hl=en-us&gl=US)

### Passo 3: Configuração Inicial

Após a instalação, abra o Kali Linux e siga as instruções para configurar seu usuário e senha. 

> **Importante**: Caso encontre algum erro, como "Error: não especificado" ou "Error: (null)", entre em contato com o **presidente da InsperSec** para obter ajuda.

## Instalação da Interface Gráfica

Para muitos testes e atividades, uma interface gráfica é útil. Para instalar a interface gráfica no Kali Linux, siga os passos abaixo:

1. No terminal do Kali, atualize os pacotes:

    ```bash
    sudo apt update
    ```

2. Instale a interface gráfica com o comando:

    ```bash
    sudo apt install -y kali-win-kex
    ```

3. Após a instalação, inicie o Kali Linux com a interface gráfica usando:

    ```bash
    kex --win -s
    ```

E pronto! Agora você tem o **Kali Linux** rodando no **Windows** com uma interface gráfica, ou seja, o **Kali Windows**.
