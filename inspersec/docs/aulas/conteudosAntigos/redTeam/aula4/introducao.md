# Introdução ao CTF (Capture The Flag)


- Essa aula é uma espécie de toolbox, onde vamos discutir as principais ferramentas e metodologias utilizadas em competições de CTF.
- Vamos abordar os tipos de CTF, categorias de desafios, ferramentas essenciais e dicas para iniciantes.
- O objetivo é fornecer uma base sólida para que você possa se preparar e participar de competições de CTF com confiança.

## O que é CTF?

**CTF (Capture The Flag)** são competições de cibersegurança onde os participantes devem encontrar "flags" (bandeiras) escondidas em sistemas, aplicações web, arquivos ou redes. Essas flags geralmente seguem um formato específico como `flag{exemplo_de_flag}` ou `SEC{string_secreta}`.

## Tipos de CTF

### 1. Jeopardy Style
- Categorias separadas (Web, Crypto, Forensics, Reverse Engineering, etc.)
- Pontuação baseada na dificuldade
- Equipes resolvem desafios independentemente

### 2. Attack-Defense
- Equipes defendem seus serviços enquanto atacam outros
- Ambiente mais realista
- Requer habilidades de defesa e ataque

### 3. King of the Hill
- Controle de uma máquina/serviço específico
- Pontuação contínua baseada no tempo de controle

## Categorias Principais

### Web Exploitation
- SQL Injection
- XSS (Cross-Site Scripting)
- CSRF (Cross-Site Request Forgery)
- Directory Traversal
- Authentication Bypass

### Cryptography
- Cifras clássicas (César, Vigenère)
- Criptografia moderna (RSA, AES)
- Hash cracking
- Análise de frequência

### Forensics
- Análise de memória
- Análise de arquivos
- Esteganografia
- Logs de sistema

### Reverse Engineering
- Análise de binários
- Engenharia reversa de malware
- Decompilação

### Binary Exploitation
- Buffer overflow
- Return-oriented programming (ROP)
- Format string bugs

## Metodologia de Approach

### 1. Reconnaissance
- Enumeration de portas e serviços
- Directory busting
- Information gathering

### 2. Scanning & Enumeration
- Identificação de vulnerabilidades
- Banner grabbing
- Service fingerprinting

### 3. Exploitation
- Exploração de vulnerabilidades encontradas
- Privilege escalation
- Persistence

### 4. Documentation
- Manter registros detalhados
- Screenshots de evidências
- Metodologia utilizada

## Ferramentas Essenciais

### Reconnaissance
- **Nmap**: Port scanning e service detection
- **Gobuster**: Directory/file brute forcing
- **Dirb**: Web content scanner

### Password Attacks
- **John the Ripper**: Password cracking
- **Hashcat**: GPU-accelerated password recovery
- **Hydra**: Network logon cracker

### Web Application Testing
- **Burp Suite**: Web application security testing
- **OWASP ZAP**: Web app scanner
- **SQLmap**: SQL injection tool

### Network Analysis
- **Wireshark**: Network protocol analyzer
- **Netcat**: Network utility
- **Tcpdump**: Packet analyzer

## Preparação do Ambiente

### Distribuições Recomendadas
- **Kali Linux**: Distribuição focada em pentesting
- **Parrot Security OS**: Alternativa ao Kali
- **Ubuntu** (com ferramentas instaladas): Para ambiente personalizado

### Configuração Básica Ubuntu
```bash
# Atualizar sistema
sudo apt update && sudo apt upgrade -y

# Instalar ferramentas essenciais
sudo apt install git curl wget python3 python3-pip -y

# Instalar build tools
sudo apt install build-essential -y
```

## Ética e Legalidade

### Princípios Fundamentais
- **Sempre obter autorização** antes de testar sistemas
- **Não acessar** dados que não pertencem a você
- **Reportar vulnerabilidades** de forma responsável
- **Respeitar** a propriedade intelectual

### Ambientes Legais para Prática
- **Plataformas Online**: HackTheBox, TryHackMe, PicoCTF
- **Laboratórios Locais**: VulnHub, DVWA, Metasploitable
- **CTFs Públicos**: OverTheWire, Root-Me

## Dicas para Iniciantes

### Mindset
- **Persistência**: Nem todo desafio será resolvido rapidamente
- **Curiosidade**: Questione tudo e explore diferentes abordagens
- **Colaboração**: Trabalhe em equipe e compartilhe conhecimento

### Organização
- Mantenha um **notebook** de comandos úteis
- **Documente** suas descobertas
- Use **screenshots** para evidências

### Aprendizado Contínuo
- Leia **writeups** de CTFs anteriores
- Pratique em **plataformas** dedicadas
- Participe de **comunidades** online
