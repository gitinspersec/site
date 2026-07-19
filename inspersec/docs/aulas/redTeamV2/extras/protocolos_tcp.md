# Protocolos, IP e Portas

*⏱️ Tempo de leitura: ~5 min*

> Material de preparo pra Aula 3. Antes de escanear e atacar um alvo, você precisa saber o que é um IP, o que é uma porta e como os dados trafegam.

## Pré-requisitos

- Ter feito a [Aula 1](../aula1/fundamentos.md)

## Como os dados trafegam: camadas

A comunicação em rede é organizada em **camadas**, cada uma cuidando de uma parte. O modelo que a internet usa na prática é o **TCP/IP**, com 4 camadas:

| Camada | Cuida de | Exemplos |
|--------|----------|----------|
| Aplicação | o que o programa fala | HTTP, DNS, SSH |
| Transporte | entregar certo e na ordem | TCP, UDP |
| Internet | achar o caminho entre redes | IP |
| Acesso à rede | o meio físico | Ethernet, Wi-Fi |

*(Existe também o modelo **OSI**, de 7 camadas, mais teórico. Pra segurança, o TCP/IP acima já resolve.)*

## IP: o endereço da máquina

Todo dispositivo numa rede tem um **endereço IP**, tipo `192.168.0.10`. É como o endereço de uma casa: é pra onde os pacotes são entregues.

- **IP privado** — usado dentro da sua rede local (ex.: `192.168.x.x`).
- **IP público** — o endereço que a internet enxerga.

## Porta: a entrada do serviço

O IP leva até a máquina. Mas uma máquina roda vários serviços ao mesmo tempo (site, e-mail, SSH...). A **porta** diz *com qual serviço* você quer falar.

Pense no IP como o prédio e na porta como o número do apartamento.

Portas e serviços padrão que você vai encontrar sempre:

| Porta | Serviço | O que é |
|-------|---------|---------|
| 21 | FTP | transferência de arquivos |
| 22 | SSH | acesso remoto (o do Bandit) |
| 25 | SMTP | envio de e-mail |
| 53 | DNS | resolve nome → IP |
| 80 | HTTP | web sem criptografia |
| 443 | HTTPS | web com TLS |
| 3306 | MySQL | banco de dados |
| 3389 | RDP | área de trabalho remota (Windows) |

> 💡 Em pentest, **enumerar as portas abertas** é o primeiro passo: cada porta aberta é um serviço que pode ter uma falha. É o que o Nmap faz.

## TCP: a entrega confiável

Na camada de transporte, o **TCP** garante que os dados chegam **completos e na ordem certa**. Antes de trocar qualquer dado, cliente e servidor fazem o **3-way handshake**:

```
Cliente                      Servidor
   |  ------- SYN ------->      |   "quero conectar"
   |  <---- SYN + ACK ----      |   "ok, pode vir"
   |  ------- ACK ------->      |   "combinado"
   |      conexão aberta        |
```

As principais flags do TCP:

| Flag | Função |
|------|--------|
| `SYN` | inicia a conexão |
| `ACK` | confirma o recebimento |
| `FIN` | encerra de forma ordenada |
| `RST` | encerra abruptamente |

## Onde isso vira ataque

O **Nmap** usa o handshake pra descobrir portas abertas. O SYN scan (`-sS`) manda um `SYN` e olha a resposta:

- veio `SYN+ACK` → porta **aberta**
- veio `RST` → porta **fechada**
- não veio nada → porta **filtrada** (provavelmente um firewall)

Ele nem completa o handshake (manda `RST` no lugar do `ACK` final), por isso é chamado de "half-open" — mais discreto. Na Aula 3 você usa isso na prática pra mapear um alvo.
