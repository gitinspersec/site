# Aula 02 — Redes de Computadores

Esta página traz uma explicação direta e curta dos quatro primeiros tópicos de redes: ideias principais, exemplos práticos e um exercício rápido por tópico.

## 1) Fundamentos e vocabulário

- O que é uma rede? Um conjunto de dispositivos (computadores, roteadores, switches, pontos de acesso) que trocam dados.
- Tipos comuns: LAN (rede local), WLAN (Wi‑Fi), WAN (rede longa distância).
- Por que importa para segurança: redes bem segmentadas limitam o alcance de um ataque.

Termos simples:
- VLAN: é como dividir uma rede física em várias redes virtuais menores.
- Segmentação: separar serviços/usuários para reduzir impactos.

Exemplo prático: se um computador for comprometido numa VLAN de usuários, a segmentação evita que ele acesse diretamente servidores críticos.

Pequeno exercício: identifique na sua rede (ou em um lab) uma VLAN e diga que serviço ela contém (ex.: VLAN 10 = usuários, VLAN 20 = servidores).

## 2) Modelos de referência 

- OSI (7 camadas) e TCP/IP (4 camadas) são maneiras de pensar na rede por partes: física, enlace, rede, transporte e aplicação.
- Para investigar, comece do físico e suba: cabo/AP → switch (L2) → roteador (L3) → aplicação (HTTP, etc.).

Por que usar isso: ajuda a achar onde o problema está — se é um cabo, uma rota, ou uma aplicação.

Pequeno exercício: para um site que não abre, escreva 3 checagens na ordem correta (por exemplo: 1) ping, 2) traceroute, 3) checar logs do servidor web).

## 3) Endereçamento e resolução 

- MAC (L2) identifica a placa de rede; IP (L3) identifica o host na rede.
- Faixas privadas IPv4 comuns: `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`.
- Sub‑rede (/24, /26): indica quantos hosts cabem numa rede.

Comandos úteis:

```powershell
arp -a          # ver tabela ARP (IP -> MAC)
ipconfig /all   # ver IPs configurados no Windows
tracert 8.8.8.8  # ver caminho até um host
```

Pequeno exercício: considere `192.168.1.0/24` — qual é o primeiro IP útil e qual é o broadcast?

## 4) Protocolos de rede e transporte 

- ICMP: usado para diagnosticar (ping). Controle externo pode ser limitado por segurança.
- TCP: protocolo com conexão — usa handshake (SYN, SYN/ACK, ACK). Importante para saber se uma conexão foi estabelecida.
- UDP: sem conexão — usado por DNS e VoIP; menos controle por parte de firewalls stateful.

Por que importa para defesa:
- Ataques como SYN flood abusam do handshake TCP; mitigações existem (SYN cookies, rate-limit).
- Serviços expostos por portas (22, 80, 443, 3389) são alvos comuns — mantenha apenas o necessário.

Pequeno exercício: liste 3 portas que seu ambiente precisa abrir e 3 que você acha seguro bloquear.

---


