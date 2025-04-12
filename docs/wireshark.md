# Wireshark: Análise de Pacotes de Rede

## Uma breve história do Wireshark

O Wireshark surgiu em 1997, quando Gerald Combs precisava de uma ferramenta para solucionar problemas de rede e, ao mesmo tempo, aprender mais sobre o funcionamento de redes. Ele então começou a desenvolver o Ethereal, o nome original do projeto.

O Ethereal foi lançado oficialmente em julho de 1998 e logo ganhou a atenção da comunidade, que passou a enviar correções, melhorias e novos recursos. Com o tempo, diversos colaboradores contribuíram com dissectors, que são responsáveis por interpretar protocolos específicos dentro dos pacotes de rede.

Em 2006, o projeto passou a se chamar Wireshark, e em 2008 foi lançada a versão 1.0, considerada a primeira versão “completa”. A conferência Sharkfest, voltada para desenvolvedores e usuários, também foi criada nesse ano.

A versão 2.0 chegou em 2015 com uma interface totalmente redesenhada. Em 2023, o projeto passou a ser mantido pela Wireshark Foundation, uma organização sem fins lucrativos que apoia o desenvolvimento da ferramenta e promove a educação sobre redes.

## O que é?

O Wireshark é um analisador de pacotes de rede. Ele permite que você "enxergue" o que está passando pela sua rede com um nível de detalhe impressionante — como se fosse um multímetro, mas em vez de medir eletricidade, ele mede e interpreta os dados que trafegam nos cabos de rede.

Antigamente, ferramentas assim eram caras e fechadas. O Wireshark mudou esse cenário por ser gratuito, open source e altamente poderoso, se tornando um dos analisadores de pacotes mais utilizados no mundo.

### Para que o Wireshark é usado?

- O Wireshark tem diversas aplicações práticas, como:

- Administradores de rede o usam para diagnosticar - problemas de conexão

- Profissionais de segurança analisam falhas e ataques de rede

- Testadores (QA) validam comunicação entre aplicações

- Desenvolvedores usam para depurar implementações de protocolo

- Estudantes o utilizam para aprender como funcionam os protocolos de rede

### Principais funcionalidades

- Funciona em Windows e Unix/Linux

- Permite capturar dados de pacotes em tempo real

- Suporta a abertura de arquivos de outros capturadores como tcpdump e WinDump

- Permite importar pacotes a partir de arquivos de texto com hex dumps

- Exibe detalhes de protocolos usados em cada pacote

- Salva e exporta dados de captura em diversos formatos

- Filtra e busca pacotes com base em múltiplos critérios

- Coloriza pacotes com base em filtros para facilitar a leitura

- Gera estatísticas úteis sobre a comunicação capturada

...

## Funcionamento básico da rede e dos protocolos

...............................


## Prática com Wireshark

### Instalação

#### Windows:

- Baixe o instalador no site oficial: https://www.wireshark.org/download.html

-  Durante a instalação, marque a opção de instalar o *WinPcap* ou *Npcap*, que são necessários para capturar pacotes em tempo real.

#### macOS:

- Rode o comando: 
```bash
brew install --cask wireshark
```

- Autorize a captura de pacotes via terminal com permissões especiais.

#### Linux:

- Rode o comando: 
```bash
sudo apt update
sudo apt install wireshark
```
- Durante a instalação, o sistema vai perguntar se você quer que usuários não-root possam capturar pacotes - é uma boa prática permitir isso.

#### Permissões

- Para capturar tráfego de rede, o Wireshark precisa de permissão de acesso à interface de rede. Em alguns sistemas, isso exige abrir o Wireshark com permissões elevadas (como sudo no Linux).

- No Linux, após a instalação, pode rodar:

```bash
sudo usermod -aG wireshark $USER
```

E depois reiniciar a sessão para conseguir capturar pacotes sem usar ```sudo```.

### Captura de Tráfego HTTP de um Site

Este exemplo demonstra como capturar e analisar o tráfego HTTP gerado ao acessar um site externo. Será utilizado o site [http://neverssl.com](http://neverssl.com), que é mantido especificamente para testes em conexões **não criptografadas** (HTTP).

#### Captura dos pacotes com Wireshark

- Iniciar o Wireshark.
- Selecionar a interface de rede que está conectada à internet (ex: `wlan0` para Wi-Fi ou `eth0` para cabo).
- Clicar em **Start Capturing** para iniciar a captura de pacotes.
- Acessar o endereço `http://neverssl.com` pelo navegador.
- No Wireshark, aplicar o filtro:

```wireshark
http
```

##### O que observar

- A requisição ```GET /``` será exibida, enviada ao domínio neverssl.com.
 
- A resposta do servidor incluirá o conteúdo da página HTML, visível diretamente nos dados do pacote.
 
- No painel de detalhes do pacote, é possível visualizar os campos Host, User-Agent, Connection, Content-Type, entre outros.

- Clicando com o botão direito em um dos pacotes HTTP e selecionando Follow > HTTP Stream, é possível visualizar a conversa completa entre cliente e servidor.

