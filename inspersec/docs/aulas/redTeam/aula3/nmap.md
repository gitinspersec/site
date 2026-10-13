# Guia Completo de Nmap: Mapeamento e Enumeração de Redes

---

## 1. O que é o Nmap e como ele funciona?

Quando você quer auditar ou invadir um sistema, o primeiro passo é responder a três perguntas básicas:

1. **O host está ligado e acessível?** (Host Discovery)
2. **Quais portas estão abertas?** (Port Scanning)
3. **Quais programas e versões estão rodando nessas portas?** (Service & OS Detection)

O Nmap faz exatamente isso: ele envia pacotes de rede brutos (TCP, UDP, ICMP) especialmente formatados para o alvo e analisa as respostas (ou a falta delas) para determinar o estado exato da máquina.

Esse handout é um informativo de como a ferramenta funciona: 

### Instalação no Linux

No Kali Linux ou Parrot OS, o Nmap já vem instalado por padrão. Em outras distribuições Debian/Ubuntu:

```bash
sudo apt update && sudo apt install nmap -y
```

---

## 2. Estrutura Básica do Comando

A sintaxe geral do Nmap é:

```bash
nmap [tipo de scan] [opções adicionais] [alvo]
```

### [Alvos]

* **Host ou IP único:**
  ```bash
  nmap 192.168.1.15
  nmap scanme.nmap.org
  ```
* **Múltiplos IPs:**
  ```bash
  nmap 192.168.1.10 192.168.1.20 192.168.1.30
  ```
* **Faixa de IPs (Range):**
  ```bash
  nmap 192.168.1.1-50
  ```
* **Sub-rede completa (Notação CIDR):**
  ```bash
  nmap 192.168.1.0/24
  ```
* **Lista de alvos a partir de um arquivo de texto:**
  ```bash
  nmap -iL alvos.txt
  ```

---

## 3. Os Estados das Portas no Nmap

Ao escanear um alvo, o Nmap classifica cada porta em um dos seguintes estados:

| Estado | Significado Técnico | O que indica para o invasor |
| --- | --- | --- |
| `open` | O serviço aceitou a conexão (respondeu com `SYN/ACK` no TCP). | Alvo pronto para enumeração e ataque. |
| `closed` | A máquina recebeu o pacote, mas não há aplicativo escutando (respondeu com `RST`). | O host está vivo, mas aquela porta não tem serviço ativo. |
| `filtered` | Os pacotes foram bloqueados/dropados sem resposta ou com ICMP inacessível. | Há um firewall, filtro de pacotes ou regras de NAT no caminho. |
| `unfiltered` | A porta está acessível, mas o Nmap não consegue dizer se está aberta ou fechada (típico de scans `-sA`). | Usado para mapear regras de firewall. |
| `open\|filtered` | O Nmap não obteve resposta (comum em varreduras UDP) e não sabe se a porta está aberta ou filtrada. | Exige testes adicionais com probes específicos. |

---

## 4. Tipos de Varredura (Scan Techniques)

### `-sS` — TCP SYN Scan (Stealth / Half-Open)
É o scan padrão e o mais utilizado quando executado com privilégios de `root`/`sudo`.
* **Como funciona:** Envia um pacote `SYN`. Se o servidor responder `SYN/ACK`, a porta está aberta. O Nmap imediatamente responde com um `RST` para derrubar a conexão **sem completar o 3-Way Handshake**.
* **Vantagens:** É rápido, eficiente e muitas aplicações antigas não registravam a conexão em logs de aplicação porque o handshake nunca terminou.
```bash
sudo nmap -sS 192.168.1.10
```

### `-sT` — TCP Connect Scan
É o scan padrão quando você **não** tem privilégios de administrador (`root`).
* **Como funciona:** Realiza o *Three-Way Handshake* completo (`SYN` $\rightarrow$ `SYN/ACK` $\rightarrow$ `ACK`) utilizando as chamadas de sistema do sistema operacional (`connect()`).
* **Desvantagem:** É mais lento e muito mais barulhento nos logs do alvo.
```bash
nmap -sT 192.168.1.10
```

### `-sU` — UDP Scan
Muitos serviços vitais rodam em UDP (DNS na porta 53, DHCP na 67, SNMP na 161).
* **Como funciona:** Envia pacotes UDP vazios. Se receber um erro ICMP `Port Unreachable`, a porta está fechada. Se não houver resposta, o Nmap marca como `open|filtered`.
* **Dica:** O scan UDP é lento por natureza; limite as portas para acelerar.
```bash
sudo nmap -sU -p 53,161,67 192.168.1.10
```

### `-sA` — TCP ACK Scan
Não descobre se as portas estão abertas, mas serve para **mapear firewalls**.
* **Como funciona:** Envia pacotes com a flag `ACK`. Se o firewall for *stateless* e deixar passar, o alvo responde com `RST` (`unfiltered`). Se for bloqueado, fica `filtered`.
```bash
sudo nmap -sA 192.168.1.10
```

---

## 5. Seleção e Controle de Portas

Por padrão, o Nmap não escaneia todas as 65.535 portas; ele escaneia apenas as **1.000 portas mais comuns**. Para alterar isso:

| Argumento | Descrição | Exemplo |
| --- | --- | --- |
| `-p <portas>` | Escaneia portas específicas ou intervalos | `nmap -p 22,80,443,8080 10.10.10.1` |
| `-p-` | Escaneia **todas as 65.535 portas** (essencial em CTF/Pentest) | `nmap -p- 10.10.10.1` |
| `-p U:53,T:80,443` | Escaneia UDP e TCP no mesmo comando | `sudo nmap -sS -sU -p U:53,T:80 10.10.10.1` |
| `-F` | Fast Scan: escaneia apenas as **100 portas mais comuns** | `nmap -F 10.10.10.1` |
| `--top-ports <N>` | Escaneia as `N` portas mais frequentes da internet | `nmap --top-ports 500 10.10.10.1` |

---

## 6. Descoberta de Hosts (Host Discovery)

Antes de perder tempo testando milhares de portas, você pode querer apenas saber **quais máquinas estão ligadas na rede**.

### `-sn` — Ping Scan (Sem escanear portas)
Apenas verifica se os hosts estão ativos.
```bash
nmap -sn 192.168.1.0/24
```
*(Em rede local Ethernet, ele envia requisições ARP; em redes externas, envia ICMP Echo + TCP SYN na 443 + TCP ACK na 80).*

### `-Pn` — Pular a Descoberta de Host (Tratar todos como online)
Muitos servidores de empresas e firewalls modernos bloqueiam pacotes de ping (ICMP). Se você rodar o Nmap comum neles, ele pode achar que a máquina está desligada e abortar o scan.
O `-Pn` força o Nmap a tentar escanear as portas diretamente:
```bash
nmap -Pn 192.168.1.10
```

---

## 7. Detecção de Versão, Sistema Operacional e Modo Agressivo

Saber que a porta 80 está aberta é bom; saber que nela roda um `Apache 2.4.49` (que tem falha de Remote Code Execution) é muito melhor.

### `-sV` — Detecção de Versão dos Serviços
O Nmap se conecta a cada porta aberta e troca mensagens (*banner grabbing* e *probes*) para descobrir o software e versão exatos:
```bash
nmap -sV 192.168.1.10
```

### `-O` — Detecção de Sistema Operacional (OS Detection)
O Nmap analisa pequenas nuances nas respostas TCP/IP da pilha de rede do alvo (*fingerprinting*) para inferir se ele roda Linux, Windows, FreeBSD, etc.:
```bash
sudo nmap -O 192.168.1.10
```

### `-A` — Modo Agressivo (All-in-One)
Ativa tudo de uma vez:
- Detecção de Sistema Operacional (`-O`)
- Detecção de Versão (`-sV`)
- Varredura de Scripts Padrão (`-sC`)
- Rastreamento de rota (`--traceroute`)

```bash
sudo nmap -A 192.168.1.10
```

---

## 8. Nmap Scripting Engine (NSE)

O **NSE** é uma das funcionalidades mais poderosas do Nmap. São scripts escritos em Lua que automatizam tarefas complexas, desde capturar metadados até explorar vulnerabilidades.

Os scripts ficam divididos em categorias: `default`, `safe`, `discovery`, `vuln`, `auth`, `exploit`, `brute`.

### `-sC` — Executar os scripts padrão
Executa a categoria `default` de scripts (não intrusivos e seguros):
```bash
nmap -sC 192.168.1.10
```

### `--script <script_ou_categoria>`
Executa scripts específicos:

* **Buscar vulnerabilidades conhecidas:**
  ```bash
  nmap --script vuln 192.168.1.10
  ```
* **Pegar cabeçalhos e título de servidor HTTP:**
  ```bash
  nmap --script http-title,http-headers -p 80,443 192.168.1.10
  ```
* **Verificar vulnerabilidade SMB (como o famoso EternalBlue - MS17-010):**
  ```bash
  nmap -p 445 --script smb-vuln-ms17-010 192.168.1.10
  ```
* **Pesquisar scripts instalados na sua máquina:**
  ```bash
  ls /usr/share/nmap/scripts/ | grep ftp
  ```

---

## 9. Velocidade, Timing e Evasão

### Timing Templates (`-T0` a `-T5`)
Controla o tempo de espera entre pacotes para ajustar a velocidade do scan:

| Flag | Nome | Uso recomendado |
| --- | --- | --- |
| `-T0` | Paranoid | Evasão extrema de IDS/IPS (extremamente lento) |
| `-T1` | Sneaky | Muito lento, para redes monitoradas |
| `-T2` | Polite | Reduz consumo de banda e carga na rede |
| `-T3` | Normal | Comportamento padrão |
| `-T4` | Aggressive | **O mais usado em pentests e CTFs** (rápido e confiável em redes modernas) |
| `-T5` | Insane | Velocidade máxima (pode perder pacotes e dar falsos negativos) |

Exemplo clássico:
```bash
nmap -T4 192.168.1.10
```

### `--min-rate <pacotes/s>`
Se você não quer esperar o Nmap se ajustar sozinho e sua rede aguenta velocidade, você pode fixar uma taxa mínima de pacotes por segundo:
```bash
nmap -p- --min-rate 1000 192.168.1.10
```

---

## 10. Salvando Resultados

Durante um teste de intrusão, **você nunca deve perder o resultado de um scan longo**. O Nmap permite exportar nos seguintes formatos:

* `-oN <arquivo>`: Saída padrão legível por humanos.
* `-oX <arquivo>`: Formato XML (ótimo para importar no Metasploit com `db_import`).
* `-oG <arquivo>`: Formato *grepable* (fácil de filtrar com `grep`, `awk` e `cut`).
* **`-oA <nome>`**: **(Recomendado)** Salva os 3 formatos de uma vez com o mesmo prefixo.

```bash
nmap -sC -sV -p- -oA scan_completo_alvo 192.168.1.10
```
*(Gera os arquivos `scan_completo_alvo.nmap`, `scan_completo_alvo.xml` e `scan_completo_alvo.gnmap`)*.

---

## 11. Casos de Uso Reais 

### Caso 1: Varredura de Descoberta de Rede Interna
> *Cenário:* Você conectou seu Kali na rede da empresa e precisa saber quem está online.
```bash
sudo nmap -sn 192.168.1.0/24 -oN hosts_vivos.txt
```

---

### Caso 2: O Scan Inicial Padrão em CTF ou Pentest
> *Cenário:* Você recebeu o IP do alvo e quer uma visão rápida e completa dos serviços padrão.
```bash
sudo nmap -sC -sV -T4 -oN recon_inicial.txt 10.10.10.100
```

---

### Caso 3: Varredura Completa de Todas as Portas (Full Port Scan)
> *Cenário:* Administradores costumam esconder painéis administrativos e portas SSH em portas altas (ex: 2222, 8080, 50000). Um scan padrão não vai achar.
```bash
# Passo 1: Descobre todas as portas abertas rapidamente
nmap -p- --min-rate 1500 -T4 10.10.10.100 -oG portas.txt

# Passo 2: Roda scripts e versões apenas nas portas encontradas (ex: 22,80,9001)
nmap -sC -sV -p 22,80,9001 10.10.10.100 -oN analise_profunda.txt
```

---

### Caso 4: Alvo que não responde a Ping (Atrás de Firewall)
> *Cenário:* O alvo não responde `ping 10.10.10.100`, mas você sabe que há servidores rodando lá.
```bash
sudo nmap -Pn -sS -sV -p 80,443,8080,8443 10.10.10.100
```

---

### Caso 5: Auditoria de Vulnerabilidades Específicas
> *Cenário:* Você encontrou uma porta 445 (SMB) aberta no Windows e quer checar se ela está vulnerável sem derrubar o servidor.
```bash
sudo nmap -p 445 --script smb-vuln* 10.10.10.100
```

---

## 12. Resumo das Flags Essenciais

| Flag | Função |
| --- | --- |
| `-sS` | SYN scan stealth (padrão com root) |
| `-sT` | Connect scan completo (padrão sem root) |
| `-sU` | UDP scan |
| `-p <portas>` | Especifica portas (`-p 80`, `-p-`, `-p 1-1024`) |
| `-sV` | Detecta versões dos serviços |
| `-O` | Detecta Sistema Operacional |
| `-sC` | Roda scripts NSE padrão |
| `-A` | Modo agressivo (`-sV`, `-O`, `-sC`, `--traceroute`) |
| `-Pn` | Pula ping e escaneia direto |
| `-sn` | Apenas ping scan (sem portas) |
| `-T[0-5]` | Define a velocidade/agressividade temporal |
| `-oA <nome>` | Salva a saída em todos os formatos |
| `-v` / `-vv` | Aumenta o nível de verbosidade na tela em tempo real |

---

## Prática Recomendada

Para treinar sem violar termos de uso ou leis:
* **Host oficial para testes:** `scanme.nmap.org` (mantido pela equipe do Nmap expressamente para testes).
* **Laboratórios locais:** Máquinas virtuais como o **Metasploitable 2** ou caixas do **Hack The Box** / **TryHackMe**.
