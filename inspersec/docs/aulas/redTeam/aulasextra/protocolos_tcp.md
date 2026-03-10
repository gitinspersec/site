# Protocolos de Rede e TCP

## Introdução

Para atuar em Red Team, é essencial entender **como os dados trafegam na rede**. Toda comunicação — desde acessar um site até um scan com Nmap — depende de protocolos de rede bem definidos. Nesta aula, vamos entender os modelos de rede, o protocolo TCP em detalhes e como isso se aplica à segurança ofensiva.

---

## Modelo OSI (7 Camadas)

O **modelo OSI** (Open Systems Interconnection) é um modelo teórico que divide a comunicação de rede em 7 camadas:

| Camada | Nome             | Função                                      | Exemplos                   |
|--------|------------------|----------------------------------------------|----------------------------|
| 1      | Física           | Transmissão de bits no meio físico           | Cabos, fibra óptica, rádio |
| 2      | Enlace de Dados  | Transferência entre nós adjacentes           | Ethernet, Wi-Fi (802.11)   |
| 3      | Rede             | Roteamento de pacotes entre redes            | IP, ICMP, ARP              |
| 4      | Transporte       | Entrega confiável de dados (ponta a ponta)   | **TCP**, UDP               |
| 5      | Sessão           | Gerencia sessões de comunicação              | NetBIOS, RPC               |
| 6      | Apresentação     | Formatação e criptografia de dados           | TLS/SSL, JPEG, ASCII       |
| 7      | Aplicação        | Interface com o usuário                      | HTTP, FTP, DNS, SSH        |

---

## Modelo TCP/IP (4 Camadas)

O modelo **TCP/IP** é o modelo utilizado na prática pela internet. Ele simplifica o OSI em 4 camadas:

| Camada TCP/IP    | Equivalente OSI     | Protocolos Principais    |
|------------------|----------------------|--------------------------|
| Acesso à Rede    | Camadas 1 e 2        | Ethernet, Wi-Fi          |
| Internet         | Camada 3             | IP, ICMP, ARP            |
| Transporte       | Camada 4             | TCP, UDP                 |
| Aplicação        | Camadas 5, 6 e 7    | HTTP, DNS, FTP, SSH      |

---

## O Protocolo TCP

O **TCP** (Transmission Control Protocol) é o protocolo de transporte mais usado na internet. Ele garante a entrega **confiável e ordenada** dos dados.

### Características do TCP

- **Orientado à conexão** — exige um handshake antes de transmitir dados
- **Confiável** — garante que todos os pacotes chegam e na ordem correta
- **Controle de fluxo** — ajusta a velocidade de transmissão
- **Controle de congestionamento** — evita sobrecarregar a rede

---

## 3-Way Handshake

Antes de qualquer troca de dados TCP, cliente e servidor realizam o **3-way handshake**:

```
Cliente                          Servidor
   |                                |
   |  -------- SYN ---------->      |   1. Cliente envia SYN
   |                                |
   |  <------ SYN + ACK ------      |   2. Servidor responde SYN+ACK
   |                                |
   |  -------- ACK ---------->      |   3. Cliente confirma com ACK
   |                                |
   |     Conexão estabelecida       |
```

### Explicação das flags

| Flag    | Significado         | Função                                          |
|---------|---------------------|--------------------------------------------------|
| `SYN`   | Synchronize         | Inicia uma conexão, sincroniza sequence numbers  |
| `ACK`   | Acknowledge         | Confirma recebimento de dados                    |
| `FIN`   | Finish              | Encerra a conexão de forma ordenada              |
| `RST`   | Reset               | Encerra abruptamente a conexão                   |
| `PSH`   | Push                | Envia dados imediatamente sem buffer             |
| `URG`   | Urgent              | Indica dados urgentes                            |

---

## Relação com Ferramentas de Segurança

### Nmap e o SYN Scan

O scan mais popular do Nmap (`-sS`) explora o 3-way handshake:

1. Nmap envia `SYN` para a porta alvo
2. Se receber `SYN+ACK` → porta **aberta**
3. Se receber `RST` → porta **fechada**
4. Se não receber nada → porta **filtrada** (firewall)

```bash
# SYN scan (precisa de root)
sudo nmap -sS 192.168.1.1

# TCP connect scan (não precisa de root)
nmap -sT 192.168.1.1

# UDP scan
sudo nmap -sU 192.168.1.1
```

O SYN scan é chamado de **"half-open"** porque o Nmap nunca completa o handshake — ele envia `RST` após receber `SYN+ACK`, evitando que a conexão seja registrada em logs do servidor.

---

## Exemplos Práticos

### Analisando tráfego com `tcpdump`

```bash
# Capturar pacotes na interface eth0
sudo tcpdump -i eth0

# Filtrar por porta 80 (HTTP)
sudo tcpdump -i eth0 port 80

# Mostrar o conteúdo dos pacotes em ASCII
sudo tcpdump -i eth0 -A port 80

# Salvar captura em arquivo .pcap (para abrir no Wireshark)
sudo tcpdump -i eth0 -w captura.pcap
```

### Usando `netcat` (nc)
```bash
# Abrir um listener na porta 4444
nc -lvnp 4444

# Conectar a um host na porta 80
nc 192.168.1.1 80

# Enviar uma requisição HTTP manualmente
echo -e "GET / HTTP/1.1\r\nHost: exemplo.com\r\n\r\n" | nc exemplo.com 80

# Transferir arquivo entre máquinas
# Máquina receptora:
nc -lvnp 9999 > arquivo.txt
# Máquina emissora:
nc 192.168.1.2 9999 < arquivo.txt
```