# Introdução ao Wireshark

Wireshark é um analisador de pacotes de rede utilizado para examinar protocolos e detectar atividades suspeitas.

## Interface do Wireshark

- **Painel de pacotes**
- **Detalhes do pacote**
- **Painel de bytes brutos**

---

## Filtros úteis

### 🎯 Filtros básicos:
- `ip.addr == X.X.X.X` — Mostra pacotes de um IP específico
- `tcp.port == 21` — Mostra tráfego na porta 21
- `http` — Mostra requisições HTTP
- `dns` — Mostra requisições/respostas DNS

### 🎯 Filtros mais avançados:
- `ftp.request.command == "STOR"` — Upload via FTP
- `tcp.flags.syn == 1 && tcp.flags.ack == 0` — Detecta SYN Scans

## 📌 Dica
Utilize **Follow TCP Stream** para ver uma sessão completa!

---

# Protocolos comuns na Forense de Rede

## FTP (File Transfer Protocol)
- Porta 21 (comando) / 20 (dados)
- **Problema:** Transmite credenciais em texto claro

## HTTP
- Porta 80 (sem TLS)
- **Problema:** Dados transmitidos sem criptografia

## DNS
- Porta 53
- **Importância:** Detecta domínios suspeitos ou comandos de C2

---

## Outros protocolos úteis:
- **SMB** — Compartilhamento de arquivos em rede
- **HTTPS** — TLS handshake, SNI visível
- **ICMP** — Ping/echo requests
- **SFTP** - Secure/SSH File Transfer Protocol


## SFTP

Para contornar o problema do FTP tradicional, que transmite dados e credenciais em texto claro, o **SFTP (SSH File Transfer Protocol)** foi criado para suprir a necessidade de uma transferência de arquivos mais segura.

O **SFTP** utiliza o protocolo **SSH** (porta padrão 22) para criar um canal criptografado de ponta a ponta, garantindo:
- **Confidencialidade** dos dados transmitidos.
- **Autenticação** robusta com chaves SSH ou senhas.
- **Integridade** durante a transferência, evitando que arquivos sejam interceptados ou alterados.

Diferente do FTP + FTPS (que usa SSL/TLS em cima do FTP tradicional), o **SFTP** é um protocolo completamente separado e mais simples de configurar em ambientes seguros.

### ⚠️ Por que isso é importante na forense?
- Em capturas de rede, o **SFTP** não revela credenciais nem arquivos em texto claro.
- Somente o handshake SSH será visível no Wireshark, e os dados transferidos estarão criptografados.

---
# Mini-CTF Forense - Wireshark

## 🎯 Objetivo:
Analisar uma captura de rede e encontrar a flag escondida.

> Enunciado: Um atacante invadiu a rede da organização e parece ter transferido arquivos e credenciais de forma insegura. Sua missão é analisar a captura de pacotes e encontrar a flag escondida.

## 📦 Arquivo:
- [forense_ctf.pcap](forense_ctf.pcap)

