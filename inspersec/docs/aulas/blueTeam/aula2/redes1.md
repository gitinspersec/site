# Fundamentos e Vocabulário

## O que é uma Rede

Uma **rede** é formalmente um **sistema distribuído**, o que significa que o processamento e o armazenamento de dados não estão concentrados em um único computador, mas espalhados por vários dispositivos interconectados.

Esses dispositivos trocam informações através de **meios de transmissão**:

- **Guiados**: como cabos de cobre (Ethernet) e fibra óptica, que conduzem o sinal fisicamente.  
- **Não guiados**: como Wi-Fi, Bluetooth ou micro-ondas, que usam ondas de rádio no ar para transmitir dados.

A comunicação entre dispositivos segue **protocolos padronizados** — regras que definem como as mensagens são formatadas, transmitidas e interpretadas.

> **Definição (Peterson & Davie)**  
> “O conjunto de regras que especifica o formato e o significado das mensagens trocadas entre entidades de comunicação.”

### Exemplos de protocolos

- **HTTPS**: combina o HTTP (comunicação web) com o TLS (criptografia e autenticação).  
- **DNS**: traduz nomes de sites em endereços IP compreendidos por roteadores.  
- **SSH**: permite acesso remoto seguro a outros computadores.

Esses protocolos são a base da comunicação digital — cada um resolve um problema específico dentro da grande teia que forma a Internet.

---

## Tipos de Rede (por escopo e tamanho de cobertura)

| Tipo | Nome | Alcance | Exemplo / Uso |
|------|------|----------|----------------|
| **PAN** | Personal Area Network | ~10 m | Bluetooth, smartwatches, fones |
| **LAN** | Local Area Network | Local | Escritórios, casas, LAN houses |
| **WLAN** | Wireless LAN | Local sem fio | Wi-Fi (802.11) |
| **MAN** | Metropolitan Area Network | Cidades | Interligação de filiais |
| **WAN** | Wide Area Network | Países / Continentes | A própria Internet |
| **SD-WAN** | Software Defined WAN | — | Roteamento via software inteligente |

---

## Topologias de Rede

- **Barramento**: todos compartilham um único meio físico.  
- **Anel**: cada dispositivo se conecta ao próximo, formando um círculo.  
- **Estrela**: todos ligados a um ponto central (switch/roteador).  
- **Malha**: cada nó se conecta a vários outros, garantindo redundância.  
- **Leaf-Spine**: comum em *data centers* modernos.

---

## Meios de Transmissão

- **Cobre**: cabos UTP Cat5e, Cat6, Cat6a (LANs).  
- **Fibra óptica**:
  - *SMF (Single Mode Fiber)* — longas distâncias.  
  - *MMF (Multi Mode Fiber)* — curtas distâncias, menor custo.  
- **Ondas de rádio**: Wi-Fi (2.4/5/6 GHz), micro-ondas, redes 5G.

Esses meios compõem a **camada física** da rede — o alicerce por onde trafegam sinais elétricos, ópticos ou eletromagnéticos.

---

## Domínios de Rede

- **Domínio de broadcast**: limite dentro do qual uma mensagem se espalha.  
- **Domínio de colisão**: área onde dois dispositivos podem competir pelo mesmo meio.  
- **Domínio de falha**: impacto de uma falha sobre os dispositivos conectados.

---

# Modelos de Referência

Modelos ajudam a visualizar e padronizar como as informações são transmitidas entre sistemas.

## Modelo OSI (Open Systems Interconnection)

| Camada | Função principal | Exemplos |
|---------|------------------|-----------|
| 7 – Aplicação | Interação com usuário e aplicativos | HTTP, DNS, SMTP, SSH |
| 6 – Apresentação | Codificação, formatação, criptografia | TLS, compressão |
| 5 – Sessão | Estabelece e encerra conexões | RPC, NetBIOS |
| 4 – Transporte | Controle de fluxo e confiabilidade | TCP, UDP, QUIC |
| 3 – Rede | Endereçamento e roteamento | IP, ICMP, OSPF |
| 2 – Enlace | Acesso ao meio e detecção de erros | Ethernet, Wi-Fi, VLAN |
| 1 – Física | Transmissão de bits | Cabos, conectores, sinais |

Cada camada é independente, comunicando-se apenas com a anterior e a seguinte.

---

## Modelo TCP/IP

Versão prática do modelo OSI, com 4 camadas:

| Camada | Equivalência OSI | Exemplos |
|---------|------------------|-----------|
| Aplicação | 5–7 | HTTP, DNS, SSH |
| Transporte | 4 | TCP, UDP |
| Internet | 3 | IP, ICMP |
| Acesso (Link + Física) | 1–2 | Ethernet, Wi-Fi |

Esse é o modelo **base da Internet moderna**.

---

# Endereçamento (L2/L3) e Resolução

## Switch

Um **switch** é um dispositivo com várias portas que conecta os elementos de uma rede para transmissão de dados, vídeo ou voz.

Ele **encaminha pacotes** com base em endereços MAC, evitando colisões e otimizando o tráfego.

### Classificação

- **Capacidade de processamento**: pacotes por segundo.  
- **Taxa de transferência**: velocidade das portas.  
- **Latência**: tempo de entrega dos dados.

### Tipos

- **L2 (Enlace de Dados)**: trabalha com endereços **MAC**, segmenta redes locais (LAN).  
- **L3 (Rede)**: usa **endereços IP**, roteia entre sub-redes e aplica **QoS**, **ACLs**, etc.

---

## Endereçamento

- **MAC**: endereço físico de cada dispositivo.  
- **ARP (IPv4)**: resolve endereços IP em MACs.  
- **Flooding**: técnica de descoberta de destino desconhecido.  
- **CAM Table**: tabela de endereços aprendidos pelo switch.  
- **IPv4 (32 bits)**: padrão antigo, limitado.  
- **IPv6 (128 bits)**: mais moderno, seguro e amplo.  
- **RFC1918 (IPv4 privado)**: define IPs não roteáveis publicamente.  
- **CIDR**: técnica que substitui classes fixas de IP, economizando endereços.  
- **NAT / CGNAT**: tradução de endereços privados ↔ públicos.  
- **ND (Neighbor Discovery)**: substituto do ARP em IPv6.

---

# Protocolos de Rede e Transporte

## ICMP (Internet Control Message Protocol)

Usado para **diagnóstico e relatório de erros** na comunicação entre dispositivos.

### Funções

- **Relatórios de erro**: mensagens “Time Exceeded”, “Destination Unreachable”.  
- **Diagnóstico**: comandos como `ping` e `traceroute`.  
- **Segurança**: ajuda a detectar caminhos suspeitos.

---

## TCP (Transmission Control Protocol)

Protocolo **confiável e orientado à conexão**.  
Usa o processo de **Three-Way Handshake**:

1. `SYN` — pedido de conexão  
2. `SYN-ACK` — aceitação  
3. `ACK` — confirmação final

### Flags importantes

`SYN`, `ACK`, `FIN`, `URG`, `PSH`, `RST`

### Controle de fluxo (janelas deslizantes)

Determina quanto dado pode ser enviado antes de aguardar confirmação (`ACK`).

### Retransmissão e MTU

Se um pacote não for confirmado, o TCP o retransmite.  
A **PMTUD (Path MTU Discovery)** ajusta dinamicamente o tamanho máximo dos pacotes.

---

## UDP (User Datagram Protocol)

Sem conexão, sem controle de erro — **rápido, mas menos confiável**.  
Usado em **streaming**, **jogos online** e **chamadas de voz**.

---

## Portas

Pontos de início e fim das conexões (Camada 4 - Transporte).

| Porta | Protocolo | Uso |
|--------|------------|-----|
| 20/21 | FTP | Transferência de arquivos |
| 80 | HTTP | Web |
| 123 | NTP | Sincronização de horário |
| 443 | HTTPS | Web segura |

---
