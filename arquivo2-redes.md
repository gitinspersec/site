# Aula 02 — Redes de Computadores (Teórica)

> **Objetivo geral:** construir uma base sólida sobre redes — de modelos e protocolos a endereçamento, roteamento, DNS, Wi-Fi, segurança e nuvem — para que o aluno entenda **como tudo se conecta** e **onde aplicar controles de segurança**.

---

## 1) Fundamentos e vocabulário

**O que é uma rede?** Conjunto de dispositivos que trocam dados por um meio de comunicação com regras (protocolos).

**Tipos de rede (escopo):**

* **LAN** (local), **WLAN** (Wi-Fi), **MAN** (metropolitana), **WAN** (longa distância), **PAN** (pessoal), **SD-WAN** (overlay inteligente sobre links WAN).

**Topologias (lógicas/físicas):**

* **Barramento**, **anel**, **estrela**, **malha**, **leaf-spine** (data centers).

**Meios de transmissão:**

* **Cobre** (UTP Cat5e/6/6a…), **fibra** (SMF/MMF), **rádio** (2.4/5/6 GHz), **micro-ondas**, **5G**.

!!! tip
Pense em **domínios**: *broadcast* (L2), *colisão* (meio compartilhado), e *falha* (impacto quando algo cai).

---

## 2) Modelos de referência

| OSI | Camada       | Exemplos                                             |
| --- | ------------ | ---------------------------------------------------- |
| 7   | Aplicação    | HTTP, DNS, SMTP, SSH                                 |
| 6   | Apresentação | TLS, codificações, compressão                        |
| 5   | Sessão       | Estabelecimento/encerramento de sessões              |
| 4   | Transporte   | TCP, UDP, QUIC                                       |
| 3   | Rede         | IP, ICMP, roteamento                                 |
| 2   | Enlace       | Ethernet (802.3), Wi-Fi (802.11), VLAN (802.1Q), STP |
| 1   | Física       | Cabos, rádio, conectores, sinais                     |

**Pilha TCP/IP (enxuta):** Aplicação | Transporte | Internet | Acesso (Link+Físico).

---

## 3) Endereçamento (L2/L3) e resolução

**L2:** **MAC** (48 bits; identifica a interface). Tabela MAC em switches aprende por origem e encaminha por destino.

**L3:** **IP** (IPv4/IPv6)

* **IPv4 privado (RFC1918):** `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`
* **CIDR/Sub-redes:** máscara define **rede/host**; *longest prefix match* no roteamento.
* **CGNAT:** `100.64.0.0/10` (NAT de operadoras).
* **IPv6:** 128 bits, notação hexadecimal, **abreviação** `::`, **link-local** `fe80::/10`, **ULA** `fc00::/7`, **global** `2000::/3`.

**Resolução de endereços:**

* **ARP** (IPv4): IP → MAC.
* **ND** (IPv6): vizinhança, descoberta de roteadores e prefixos.

!!! warning
**NAT não é controle de segurança.** Ele esconde endereços, mas não substitui firewall/ACL.

---

## 4) Protocolos de rede e transporte

**ICMP:** diagnósticos (echo, *time exceeded*, *unreachable*). Essencial para *troubleshooting*.

**TCP (orientado à conexão):**

* *Handshake* em 3 vias, janelas, ACK, retransmissão, controle de congestionamento, **MTU/PMTUD**.
* Flags (SYN/ACK/FIN/RST/PSH/URG).

**UDP (não orientado):**

* Sem confiabilidade nativa; útil para latência baixa (VoIP, DNS, QUIC/HTTP/3).

**Portas (exemplos):**

* 22/SSH, 53/DNS, 80/HTTP, 123/NTP, 443/HTTPS, 3306/MySQL, 5432/Postgres, 6379/Redis, 3389/RDP.

---

## 5) Comutação em L2 (Switching)

* **Switch**: encaminha quadros por **MAC table**; desconhecido → *flood*.
* **Domínios**: *broadcast* (ARP, DHCP), *colisão* (virtual em hubs/meios compartilhados).
* **Spanning Tree (STP/RSTP/MSTP):** evita loops em L2.
* **VLAN (802.1Q):** segmenta L2; **porta access** (uma VLAN), **trunk** (múltiplas VLANs, *tagged*).
* **LACP/EtherChannel:** agrega links (maior banda/HA).
* **Port Security/802.1X:** controla quem entra no switch.

---

## 6) Roteamento em L3

* **Estático** vs **dinâmico**.
* **Distance Vector** (ex.: RIP), **Link State** (ex.: OSPF/IS-IS).
* **BGP** (entre AS): política, caminho, *peering*, internet pública e *anycast*.
* **ECMP**: múltiplos caminhos de mesmo custo.
* **VRRP/HSRP**: gateway virtual para alta disponibilidade.

---

## 7) DNS — o “catálogo” da internet

**Fluxo:** *resolver* recursivo → raiz → TLD → autoritativo → cache (TTL).

**Registros comuns:**

* **A/AAAA** (IPv4/IPv6), **CNAME** (alias), **MX** (e-mail), **TXT** (políticas/SPF), **NS/SOA**, **PTR** (reverso), **SRV**, **CAA**.

**Boas práticas:**

* TTL coerente, autoritativos redundantes, DNSSEC (validação), monitoramento de falhas de resolução.

---

## 8) HTTP/S e TLS (visão de alto nível)

* **HTTP métodos:** GET/POST/PUT/PATCH/DELETE; **status:** 1xx–5xx.
* **TLS:** certificado (cadeia/CA), *handshake*, **SNI**, **ALPN**, *forward secrecy*.
* **HTTP/2:** multiplexação, compressão de cabeçalhos.
* **HTTP/3 (QUIC/UDP):** latência menor e melhor performance em perdas.

---

## 9) Wi-Fi (802.11)

* **Bandas:** 2.4 GHz (alcance >, interferência >), 5 GHz/6 GHz (capacidade >).
* **Padrões:** b/g/n/ac/ax/be (cresce throughput/eficiência).
* **Segurança:** WPA2-PSK, **WPA3-SAE**, **802.1X (EAP)**.
* **Parâmetros de qualidade:** RSSI, **SNR**, canais, largura de canal, roaming, densidade de APs.

!!! tip
Em ambientes densos, prefira **canais não sobrepostos**, potência adequada e largura de canal moderada (evita interferência).

---

## 10) QoS e desempenho

* **Métricas:** **latência**, **jitter**, **perda**, **throughput**.
* **Bufferbloat**: latência alta por buffers excessivos.
* **QoS:** marcação (**DSCP/CoS**), *shaping*, *policing*, filas (FIFO, WFQ, CBWFQ, LLQ).
* **BDP (Bandwidth-Delay Product):** quanto de dado “em voo” maximiza o enlace.

---

## 11) Serviços auxiliares

* **DHCP:** DORA (Discover/Offer/Request/Ack), reservas, **relay** (IP Helper).
* **NTP:** sincroniza relógios (logs e TLS dependem disso).
* **SNMP:** monitoramento (prefira **v3**), **Syslog** para centralizar eventos.
* **IPAM/CMDB:** inventário e rastreabilidade de endereços/ativos.

---

## 12) Segurança de rede (essenciais)

* **Firewalls L3/L4 (stateful)**, **NGFW** (aplicação), **WAF** (HTTP), **IDS/IPS**, **NDR**.
* **Segmentação:** VLAN, VRF, microsegmentação, **Zero Trust**.
* **VPNs:** **IPsec** (IKEv2/ESP), **SSL VPN**, **WireGuard** (chaves curtas, simples).
* **Proxies e balanceadores:** *forward/reverse*, L4 vs L7, *health checks*, **anycast** em CDNs.
* **Ameaças comuns:** spoofing, MITM, **DoS/DDoS** (volumétrico, protocolo, aplicação), **SYN flood**.
* **Mitigações:** *rate-limit*, *SYN cookies*, scrubbing, ACLs, listas de reputação, *WAF rules*, *geo/IP*.

!!! warning
Seguração efetiva = **camadas + processos**. Não confie apenas em um controle (ex.: “tem WAF, logo está tudo bem”).

---

## 13) Nuvem e contêineres (conceitos de rede)

**Nuvem (IaaS):**

* **VPC/VNet**, sub-redes, **route tables**, **Internet/NAT Gateways**, **Security Groups** (estadoful por instância), **NACLs** (estateless por sub-rede).
* **Peering/Transit/Direct Connect**: conexões entre redes e on-prem.

**Kubernetes (CNI):**

* **IPs de Pod** ≠ **IPs de Node**; *overlay* de rede (Calico/Flannel/Cilium).
* **Services:** ClusterIP, NodePort, LoadBalancer, **Ingress** (L7).
* **NetworkPolicies:** controle de tráfego L3/L4 entre Pods/Namespaces.

**Docker (básico):**

* Redes **bridge**, **host**, **overlay**; *port mapping* e DNS interno.

---

## 14) Padrões de arquitetura

* **Hub-and-spoke** vs **full-mesh** vs **leaf-spine**.
* **HA**: *active-active* vs *active-standby*, *state sync*, gateways virtuais (VRRP/HSRP).
* **Anycast**: um IP, múltiplos pontos de presença (DNS, CDN, edge).

---

## 15) Troubleshooting (abordagem conceitual)

1. **Do físico ao lógico** (camada por camada): link → L2/VLAN → IP/rota → DNS → app.
2. **Isolamento:** “funciona local?” “mesma rede?” “outra rede?” “internet?”.
3. **Padrões de falha clássicos:**

   * MTU/fragmentação (PMTUD quebrado),
   * assimetria de rotas,
   * DNS desatualizado/TTL baixo,
   * **NAT hairpin** (acesso ao próprio público de dentro).

---

## 16) Tabelas de referência rápida

**Faixas privadas (IPv4):**

* `10.0.0.0/8` | `172.16.0.0/12` | `192.168.0.0/16`
  **Loopback:** `127.0.0.0/8` | **Link-local:** `169.254.0.0/16`
  **Multicast:** `224.0.0.0/4` | **CGNAT:** `100.64.0.0/10`

**Registros DNS (resumo):** A/AAAA, CNAME, MX, TXT, NS, SOA, SRV, PTR, CAA.

**Portas comuns:** 22/SSH, 53/DNS, 80/HTTP, 123/NTP, 389/LDAP, 443/HTTPS, 3389/RDP.
