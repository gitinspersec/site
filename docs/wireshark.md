# Wireshark: Análise de Pacotes de Rede

## O que é Wireshark?

O Wireshark é um analisador de pacotes de rede. Ele permite que você "enxergue" o que está passando pela sua rede com um nível de detalhe impressionante — como se fosse um multímetro, mas em vez de medir eletricidade, ele mede e interpreta os dados que trafegam nos cabos de rede.

Antigamente, ferramentas assim eram caras e fechadas. O Wireshark mudou esse cenário por ser gratuito, open source e altamente poderoso, se tornando um dos analisadores de pacotes mais utilizados no mundo.

### Para que o Wireshark é usado?

- O Wireshark tem diversas aplicações práticas, como:

- Administradores de rede o usam para diagnosticar problemas de conexão

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

### O que são protocolos?

Pense em protocolos como regras de conversação entre dispositivos de rede. Assim como humanos precisam de um idioma comum para se entenderem, computadores usam protocolos como TCP, UDP, HTTP e DNS, para trocarem dados corretamente.

#### Diferença entre TCP e UDP

- TCP (Transmission Control Protocol) é como uma conversa por mensagens com confirmação: você manda um “oi”, espera resposta, e só continua quando tem certeza de que a outra pessoa te entendeu. É confiável, mas mais lento.

- UDP (User Datagram Protocol) é como gritar algo na rua: você manda uma mensagem e torce para que alguém escute — sem garantias de entrega. É rápido, mas não confiável.

#### HTTP e HTTPS

- HTTP (e sua versão segura, HTTPS) é o protocolo de aplicação que usamos para acessar páginas web. Ele funciona sobre TCP, e define como o navegador pede e recebe arquivos (como HTML, CSS, imagens).

- HTTPS usa criptografia TLS para proteger os dados, o que dificulta a interceptação (sniffing) por ferramentas como o Wireshark — mas ainda é possível ver metadados (IP, domínios SNI, tamanho dos pacotes, etc.).


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

### Captura de Tráfego UDP (DNS)

O protocolo UDP é usado por diversas aplicações, especialmente quando a velocidade é mais importante que a confiabilidade. Um dos exemplos mais comuns é o DNS (Domain Name System), que geralmente utiliza UDP na porta 53.

#### Captura dos pacotes com Wireshark

- Iniciar o Wireshark.
- Selecionar a interface de rede conectada à internet.
- Clicar em **Start Capturing**.
- No navegador, acessar qualquer site (ex: `https://www.google.com.br/`) para gerar uma consulta DNS.
- No Wireshark, aplicar o filtro:

```bash
udp.port == 53
```

- Observar os pacotes DNS sendo enviados e recebidos.

**Dica** : Para gerar múltiplas consultas DNS, é possível limpar o cache DNS local e acessar novos domínios, como:

#### Linux/macOS:
```bash
sudo systemd-resolve --flush-caches
```

#### Windows (CMD como administrador):
```bash
ipconfig /flushdns
```