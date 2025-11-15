# Aula 02 — Redes de Computadores (Parte II)

> Continuação direta de `redes1.md`. Este capítulo assume domínio dos fundamentos e aprofunda aplicações práticas, segurança e operações modernas de rede.

## Seção 0 — Ponte com Redes I

Esta parte pressupõe domínio dos blocos 1–7 de [`redes1.md`](../aula2/redes1.md): definição de redes, modelos OSI/TCP-IP, meios físicos e protocolos básicos (ICMP, TCP, UDP). Aqui avançamos da **gramática** para a **sintaxe aplicada**, explorando como projetar, proteger e operar redes reais.

### 0.1 Recapitulação expressa

- **Vocabulário comum:** tipos de rede (PAN → WAN), topologias (estrela, malha, leaf-spine) e domínios (broadcast, colisão, falha).
- **Camadas:** habilidade de mapear protocolos às camadas OSI/TCP-IP.
- **L2/L3 essenciais:** switches, MAC, ARP/ND, noções de IPv4/IPv6 e portas conhecidas.
- **Ferramentas de apoio:** tabelas rápidas e `!!! tip`/`!!! warning` utilizados em Redes I continuam válidos.

Se algum dos itens acima não estiver claro, retorne às seções correspondentes antes de avançar. O restante do material parte dessas bases sem reexplicar conceitos introdutórios.

### 0.2 Progressão planejada

| Parte I (Redes I) | Parte II (este capítulo) | Resultado esperado |
|-------------------|--------------------------|--------------------|
| Endereçamento básico (CIDR introdutório) | CIDR avançado, VLSM, NAT/CGNAT, IPv6 profundo | Capacidade de desenhar planos de endereçamento para campus e provedores. |
| Switch L2 e domínios | Segmentação (VLAN, STP, Port-Sec) | Projetar LANs resilientes e seguras. |
| Roteamento básico | Protocolos dinâmicos, BGP, HA | Operar redes multi-site e com Internet redundante. |
| DNS visão geral | DNS hierárquico, ataques e defesas modernas | Explicar riscos de spoofing e propor mitigação (DNSSEC, DoH). |

### 0.3 Pré-labs e ferramentas

- **Simuladores:** GNS3/EVE-NG para testar roteamento e VLANs; Wireshark para capturar DNS/QUIC.
- **Documentação recomendada:** RFCs citadas ao longo do texto, `Computer Networks` (Peterson & Davie), `Practical BGP` (Cloudflare Radar posts).
- **Checklist pessoal:** confirme acesso a pelo menos um laboratório virtual ou físico com dois roteadores virtuais, um switch gerenciável e uma VM cliente.

!!! tip
	Mantenha um caderno (ou documento versionado) com diagramas, sub-redes criadas e decisões de projeto. Isso vira ativo para atividades práticas e para o SOC quando precisar responder incidentes em redes complexas.

## Seção 1 — Endereçamento avançado

### 1.1 CIDR e VLSM sem sustos

O CIDR (Classless Inter-Domain Routing) permite escolher qualquer tamanho de prefixo, quebrando as antigas classes A/B/C. A fórmula para hosts úteis em IPv4 permanece:

$$n_{hosts} = 2^{(32-\text{prefix})} - 2$$

Exemplo rápido: `/27` ⇒ $2^{5}-2 = 30$ hosts.

**VLSM (Variable Length Subnet Mask)** habilita dividir um bloco pai em sub-redes de tamanhos diferentes, maximizando o aproveitamento. Fluxo sugerido:

1. Ordene sub-redes pelo tamanho (da maior para a menor).
2. Aloque cada sub-rede alinhando o prefixo ao limite binário correto.
3. Atualize tabela de roteamento (estático ou dinâmico) sempre que criar novas divisões.

| Cenário | Bloco disponível | Sub-redes necessárias | Planejamento | Observações |
|---------|------------------|-----------------------|-------------|-------------|
| Campus com 4 prédios | `10.40.0.0/22` | Core (100 hosts), Laboratórios (60), IoT (30), Gestão (20) | `/25`, `/26`, `/27`, `/27` respectivamente | Reserve `/30` adicionais para enlaces roteados. |

!!! tip
	Planilhas compartilhadas ou ferramentas como `ipcalc`, `subnetcalc` e módulos do NetBox ajudam a versionar o plano de endereçamento e evitam sobreposições.

### 1.2 NAT, CGNAT e limites operacionais

- **NAT44 tradicional:** traduz IPs privados RFC1918 para um ou mais IPs públicos. Usado em bordas corporativas e residenciais.
- **PAT (Port Address Translation):** variação mais comum; múltiplos hosts compartilham um IP público, diferenciados por portas de origem.
- **NAT64 / DNS64:** ponte entre clientes IPv6 e serviços IPv4 legados.
- **CGNAT (Carrier Grade NAT):** operadoras usam o bloco `100.64.0.0/10` para NAT em massa, adicionando mais uma camada antes da Internet.

| Tipo | Bloco típico | Vantagem | Custo/risco |
|------|--------------|----------|-------------|
| NAT corporativo | RFC1918 → IP público | Conserva endereços, oculta topologia interna | Pode quebrar aplicações P2P, exige regras de port-forwarding. |
| CGNAT | `100.64.0.0/10` | Permite ISP atrasar adoção plena de IPv6 | Dificulta auditoria, bloqueios por reputação podem afetar clientes legítimos. |
| NAT64 | IPv6-only → IPv4 | Facilita redes IPv6 puras em campus | Dependência de tradutor e DNS64 bem configurados. |

!!! warning
	**NAT não é controle de segurança.** Ele reduz exposição direta, mas não substitui firewall stateful, IDS/IPS ou políticas Zero Trust.

### 1.3 IPv6 em produção

Principais blocos:

- **Link-local:** `fe80::/10` — obrigatório em cada interface, usado por ND.
- **ULA (Unique Local Address):** `fc00::/7` — equivalente aos privados, porém roteáveis internamente.
- **Global Unicast:** `2000::/3` — espaço público.

Conceitos críticos:

- **Representação:** grupos hexadecimais de 16 bits; zeros consecutivos podem ser comprimidos (`2001:db8::1`).
- **SLAAC vs DHCPv6:** SLAAC (Stateless Address Autoconfiguration) cria endereços a partir dos anúncios de roteador (RA). DHCPv6 adiciona informações extras (DNS, domínio). É comum combinar ambos.
- **ND (Neighbor Discovery, RFC 4861):** substitui ARP, inclui descoberta de roteadores, prefixos e detecção de endereços duplicados.
- **Prefix delegation:** ISPs entregam `/56` ou `/48` para clientes; em campus, distribua `/64` por VLAN (recomendação do RIPE/ARIN).

| Tarefa | IPv4 | IPv6 |
|--------|------|------|
| Descobrir gateway | ARP para IP do gateway | Neighbor Solicitation para endereço link-local do roteador |
| Multicast equivalente ao broadcast | `255.255.255.255` | `ff02::1` (todos os nós) / `ff02::2` (todos os roteadores) |
| Reserva de host | Endereços finais (rede/broadcast) | Não é necessário reservar (não há broadcast) |

!!! tip
	Automatize a publicação de prefixos IPv6 no NetBox/CMDB e gere templates de configuração (Terraform/Ansible) para manter consistência e evitar erros humanos.

## Seção 2 — Comutação e segmentação avançada

### 2.1 VLANs e domínios controlados

**VLAN (802.1Q)** permite criar múltiplos domínios L2 sobre o mesmo hardware.

| Tipo de porta | Descrição | Uso típico |
|---------------|-----------|-----------|
| Access | Pertence a uma única VLAN, sem tags | Estações de trabalho, impressoras |
| Trunk | Carrega várias VLANs com tags 802.1Q; pode ter VLAN nativa | Enlaces entre switches, switches ↔ roteadores |
| Hybrid | Mistura access + trunk (alguns vendors) | Voz + dados no mesmo cabo |

Boas práticas:

- Use VLANs únicas por finalidade (usuários, servidores, IoT, VoIP) para limitar broadcast e incidentes.
- Documente VLAN ID ↔ segmento IP ↔ ACL associada.
- Em ambientes VoIP, configure **Voice VLAN** dedicada com QoS (CoS 5/EF).

### 2.2 Spanning Tree e prevenção de loops

Redes com múltiplos enlaces redundantes precisam de STP/RSTP/MSTP para evitar loops L2 que saturam o domínio de broadcast.

- **STP (802.1D):** original, convergência lenta (30–50 s).
- **RSTP (802.1w):** convergência sub-1s, compatível com STP.
- **MSTP (802.1s):** grupos de VLANs com árvores diferentes, útil em campus grandes.

Checklist mínimo:

1. Defina manualmente o **Root Bridge** (menor prioridade) nos switches core.
2. Habilite **BPDU Guard** em portas access para desligá-las se receberem BPDUs (previne loops via switch não gerenciado).
3. Use **PortFast** em portas de usuário para reduzir tempo de ativação.

!!! warning
	Loops L2 derrubam a rede inteira em segundos. Ferramentas como `storm-control` e `loop guard` devem ser padrão, especialmente em redes com switches distribuídos em prédio/campus.

### 2.3 LACP e enlaces agregados

**LACP (802.1AX)** agrega múltiplos links físicos em um único link lógico (**Port-Channel/EtherChannel**). Benefícios:

- Aumento de throughput (soma das portas, respeitando algoritmo de hash).
- Tolerância a falhas: se uma porta cair, as demais continuam operando.
- Consistência de configuração (uma interface lógica para aplicar ACL/QoS).

Recomendações:

- Sempre usar LACP ativo ↔ ativo para negociar automaticamente.
- Garantir compatibilidade de hashing nas duas pontas (MAC, IP, L4).
- Evitar misturar portas com velocidades diferentes; o menor denominador comum prevalece.

### 2.4 Controle de acesso em L2

- **Port Security:** limita número de MACs por porta, pode fixar MACs conhecidos ou aprender dinamicamente e gravar em running-config.
- **Dynamic ARP Inspection (DAI):** valida ARP contra tabela DHCP Snooping para impedir ARP spoofing.
- **DHCP Snooping:** constrói banco de IP↔MAC↔VLAN e bloqueia servidores DHCP não autorizados.
- **802.1X + NAC:** autenticação por porta usando EAP (PEAP, EAP-TLS). Integra com RADIUS para aplicar VLAN dinâmica ou políticas específicas.

| Recurso | Benefício Blue Team | Observações |
|---------|--------------------|-------------|
| Port Security | Bloqueia hubs/switches clandestinos e ataques CAM overflow | Exige manutenção quando troca-se hardware do usuário |
| 802.1X | Garante que apenas dispositivos autorizados ingressam na rede | Pode exigir supplicants em dispositivos IoT; use MAB (MAC Auth Bypass) como fallback controlado |
| NAC posture | Verifica antivírus, patches, etc. antes de liberar acesso | Planeje exceções para equipamentos críticos (OT, impressoras) |

!!! tip
	Combine 802.1X + VLANs dinâmicas: usuários autenticados caem em VLAN de produção, convidados em VLAN de acesso restrito com portal cativo.

## Seção 3 — Roteamento em L3

### 3.1 Estático vs dinâmico

| Critério | Estático | Dinâmico |
|----------|----------|----------|
| Configuração | Manual (route statements) | Protocolos que trocam rotas automaticamente |
| Escalabilidade | Limitada; indicado para poucos enlaces | Alta; ideal para redes com dezenas/centenas de rotas |
| Convergência | Dependente de scripts/automação | Automática e geralmente mais rápida |
| Visibilidade | Sem métricas adicionais | Suporta custo, largura de banda, políticas |

Estático continua útil para rotas de default/backup ou túneis específicos. Já protocolos dinâmicos oferecem adaptação automática a falhas.

### 3.2 Protocolos de interior (IGP)

- **RIP v2:** hop-count, simples, porém limitado (máx 15 hops). Útil apenas em ambientes muito pequenos ou laboratoriais.
- **OSPF (RFC 2328) / OSPFv3 (RFC 5340):** link-state com cálculo SPF (Dijkstra). Suporta áreas, summarization e autenticação. Escolha natural para LANs e redes campus.
- **IS-IS:** link-state amplamente usado em provedores e grandes data centers; opera diretamente sobre camada 2 (CLNS).

Elementos-chave do OSPF:

1. **Áreas:** backbone (Area 0) + áreas regulares/Stub/NSSA para reduzir LSAs.
2. **Tipos de roteadores:** DR/BDR em redes broadcast, ABR (interliga áreas), ASBR (redistribui rotas externas).
3. **Autenticação:** MD5 ou SHA para evitar LSAs maliciosos.

### 3.3 BGP (EGP) em alto nível

**BGP (Border Gateway Protocol)** opera entre Sistemas Autônomos (AS) na Internet ou internamente (iBGP) em redes grandes. Características:

- **Baseado em políticas:** decisões de roteamento consideram atributos (Local Preference, AS Path, MED, Communities).
- **Escalável:** suporta centenas de milhares de rotas.
- **Anycast e multihoming:** permite anunciar o mesmo prefixo em múltiplas localidades para distribuir tráfego/absorver DDoS.

Boas práticas (RFC 7454):

- Filtrar prefixos privados/indesejados (RFC1918, default) nas bordas.
- Implementar **BFD** para detecção rápida de falhas em links BGP.
- Usar **ROA/RPKI** para validar que um AS está autorizado a anunciar determinado prefixo.

### 3.4 Equal-Cost Multi-Path (ECMP)

ECMP permite usar vários caminhos com custo igual para balancear tráfego em IGPs ou BGP. Requisitos:

- Protocolos precisam calcular múltiplos next-hops com mesma métrica.
- Equipamento deve suportar hashing baseado em campos L3/L4 para distribuir fluxos.

Benefícios: melhora throughput agregado, reduz convergência (já que caminhos pré-calculados estão instalados) e aumenta tolerância a falhas.

### 3.5 Alta disponibilidade de gateway (VRRP/HSRP)

- **HSRP (Cisco)** e **VRRP (RFC 5798)** criam um gateway virtual compartilhado por dois ou mais roteadores.
- Um roteador atua como **active/master**, respondendo ao IP/MAC virtual; o outro fica em standby.
- `preempt` garante que o roteador com prioridade mais alta reassuma quando voltar.

Checklist HA:

1. Use links redundantes e fontes de energia independentes.
2. Sincronize tabelas ARP/CAM com timers coerentes para evitar flapping.
3. Combine com **tracking** (ex.: decremento de prioridade se o link upstream cair).

!!! tip
	Automação com Ansible/Terraform + NetBox como fonte de verdade evita divergência entre tabelas de roteamento planejadas e configuradas. Versione mudanças de topologia como código (GitOps para rede).

## Seção 4 — DNS aprofundado

### 4.1 Arquitetura hierárquica

O DNS é uma árvore invertida distribuída:

1. **Root Zone (`.`):** mantida por 13 identificadores lógicos (A–M) com centenas de instâncias anycast globais.
2. **TLDs (Top-Level Domains):** `.com`, `.org`, `.gov`, ccTLDs como `.br`, `.uk`.
3. **Second-Level Domains:** `inspersec.com.br`, `example.org` — registrados por organizações.
4. **Subdomínios:** `www`, `mail`, `api` — administrados pelo dono do domínio.

Servidores envolvidos:

- **Recursive Resolver:** geralmente operado pelo ISP ou pela própria organização.
- **Root / TLD / Autoritativos:** respondem iterativamente apontando para a próxima autoridade.

### 4.2 Fluxo recursivo vs iterativo

| Passo | Agente | Tipo de consulta | Ação | Resultado |
|-------|--------|------------------|------|-----------|
| 1 | Cliente (stub resolver) | Recursiva | Solicita `www.exemplo.com` ao resolvedor recursivo | Resolvedor assume responsabilidade |
| 2 | Resolvedor recursivo | Iterativa | Pergunta a um servidor raiz | Recebe endereços do TLD `.com` |
| 3 | Resolvedor recursivo | Iterativa | Pergunta ao servidor do TLD `.com` | Recebe o NS autoritativo de `exemplo.com` |
| 4 | Resolvedor recursivo | Iterativa | Pergunta ao servidor autoritativo | Recebe registro A/AAAA final |
| 5 | Resolvedor recursivo | Resposta final | Armazena em cache (TTL) e retorna ao cliente | Navegador se conecta ao IP |

**TTL (Time-To-Live):** controla quanto tempo o cache é válido. TTLs longos reduzem tráfego, mas atrasam mudanças; TTLs curtos (ex.: migração ou resposta a incidentes) permitem ajustes rápidos, porém aumentam carga em autoritativos.

### 4.3 Ataques clássicos

1. **Cache Poisoning:** atacante forja resposta DNS antes do autoritativo legítimo, envenenando o cache com IP malicioso (ex.: ataque de Kaminsky/2008). Mitigações: randomização de porta de origem + TXID, DNSSEC, limitar resolvers abertos.
2. **Amplificação DNS:** consultas pequenas (ex.: `ANY`, `DNSSEC`) geram respostas grandes. Com spoofing de IP, trafego refletido satura a vítima. Mitigações: desativar `ANY`, limitar recursion, usar rate-limit em autoritativos.
3. **Túnel DNS:** dados exfiltrados via registros TXT/CNAME. Detecte por tamanhos anormais de consultas/respostas e domínios DGA.

### 4.4 DNSSEC e cadeia de confiança

- **DNSKEY:** contém chave pública da zona; usada para validar assinaturas.
- **RRSIG:** assinatura digital dos RRsets.
- **DS (Delegation Signer):** hash da chave da zona filha publicado na zona pai.

Fluxo de validação:

1. Resolver possui `trust anchor` da raiz (chave pública).
2. Ao consultar um TLD assinado, valida o `DS` e `DNSKEY` do filho.
3. Propaga a confiança até o domínio final; se qualquer assinatura falhar, a resposta é descartada.

Benefícios: impede que respostas forjadas (cache poisoning) sejam aceitas por resolvers validadores. Limitação: não oferece confidencialidade (dados ainda trafegam em texto puro).

### 4.5 Privacidade moderna — DoT e DoH

| Tecnologia | Porta | Transporte | Prós | Desafios |
|------------|-------|-----------|------|----------|
| **DoT (DNS over TLS)** | 853 | TLS direto sobre TCP | Simples, dedicado; fácil de bloquear/permitir | Requer abertura explícita da porta 853 em firewalls |
| **DoH (DNS over HTTPS)** | 443 | HTTPS/HTTP2/HTTP3 | Indistinguível do tráfego web; melhora privacidade | Pode driblar filtros corporativos, dificulta interceptação lícita |

Implicações para Blue Team:

- Atualizar proxies/firewalls para inspecionar requisições DoH autorizadas e bloquear endpoints não suportados.
- Registrar logs de resolvedores locais mesmo quando clientes usam DoT/DoH internos.
- Em incidentes, correlacionar SNI/ESNI, JA3, domínios consultados e telemetria de endpoint para identificar uso malicioso.

!!! tip
	Mantenha autoritativos com **Anycast** e monitoramento ativo (latência, falhas de assinatura DNSSEC). Ferramentas como `dnsperf`, `dnsviz` e `delv` ajudam a validar configurações antes de publicar.

## Seção 5 — Protocolos de aplicação segura (HTTP/TLS/QUIC)

### 5.1 Evolução do HTTP

| Versão | Características | Benefícios | Pontos de atenção |
|--------|-----------------|------------|-------------------|
| HTTP/1.1 | Conexões persistentes, pipeline limitado | Simplicidade, compatibilidade universal | Head-of-line blocking, múltiplas conexões paralelas |
| HTTP/2 | Multiplexação em uma única conexão TCP + HPACK (compressão de cabeçalhos) | Reduz latência e overhead | Sensível a perda (bloqueio no TCP) |
| HTTP/3 | Baseado em QUIC (UDP + TLS 1.3 embutido) | Recuperação rápida, 0-RTT, melhor mobilidade | Firewalls precisam permitir UDP 443; visibilidade reduzida |

> Precisa revisar camadas? Consulte a Seção 4 (Modelos de Referência) em [`redes1.md`](../aula2/redes1.md) para contextualizar como HTTP/TLS se encaixam na pilha.

### 5.2 TLS em camadas

Processo (TLS 1.3):

1. **ClientHello:** lista suites suportadas, versão TLS, extensões (SNI, ALPN).
2. **ServerHello:** seleciona suite, envia certificado e chave pública.
3. **Key Schedule:** derivação de chaves simétricas usando Diffie-Hellman (ECDHE) → sigilo perfeito (**PFS**).
4. **Finished:** ambos passam a criptografar dados da sessão.

Implicações Blue Team:

- **SNI/ALPN:** úteis para identificar aplicações mesmo com TLS. HTTP/3 usa QUIC, então observe `ALPN = h3`.
- **Certificados:** monitore validade, cadeia e CAs autorizadas (registro CAA ajuda).
- **mTLS (mutual TLS):** considerar para APIs internas e comunicação entre microserviços críticos.

### 5.3 QUIC e inspeção

- Opera sobre UDP 443 com controle de congestionamento próprio; cada fluxo tem IDs independentes, evitando head-of-line blocking.
- Inclui TLS 1.3 nativamente, reduzindo handshakes (0-RTT para reconexões).
- Em redes corporativas, valide se firewalls/IDS suportam inspeção de QUIC ou se será necessário forçar fallback para HTTP/2 em certos domínios.

!!! tip
	Para visibilidade, use telemetria de **JA3/JA4** (impressões digitais de TLS) e monitore mudanças súbitas — pode indicar malware tentando disfarçar C2 como tráfego HTTPS legítimo.

## Seção 6 — Wi-Fi e mobilidade

### 6.1 Bandas e padrões

| Banda | Padrões | Características | Uso recomendado |
|-------|---------|-----------------|-----------------|
| 2,4 GHz | 802.11b/g/n | Maior alcance, mais interferência (Bluetooth, micro-ondas), apenas 3 canais não sobrepostos | IoT, áreas abertas com poucos APs |
| 5 GHz | 802.11a/n/ac/ax | Mais canais, DFS, throughput maior | Escritórios, ambientes densos |
| 6 GHz | 802.11ax (Wi-Fi 6E) / 802.11be (Wi-Fi 7) | Larguras de canal largas (80/160 MHz), baixa interferência | Laboratórios e aplicações de alta largura de banda |

### 6.2 Planejamento de capacidade

- Dimensione APs por **densidade de clientes** (quantidade de dispositivos simultâneos) e não apenas por área.
- Use **site survey** (Ekahau, AirMagnet) para validar cobertura, RSSI e SNR.
- Configure largura de canal adequada (20/40 MHz em ambientes densos) para reduzir interferência co-canal.

### 6.3 Segurança Wi-Fi

- **WPA3-SAE:** substitui WPA2-PSK, resistente a ataques de dicionário offline.
- **802.1X (EAP-TLS/PEAP):** autenticação individual com RADIUS; permita VLAN dinâmica ou grupos ACL.
- **PMF (Protected Management Frames):** evita desassociação forjada.
- Desabilite WPS e oculta SSIDs convidados atrás de captive portal isolado.

!!! warning
	Reaproveitar a mesma senha PSK por anos facilita engenharia social e ataques passivos (captura de handshake). Estabeleça rotação periódica e considere WPA3-Enterprise sempre que possível.

### 6.4 Telemetria para o SOC

- Colete eventos de roaming, falhas de autenticação e tentativas de associação a BSSIDs inexistentes (pode indicar Evil Twin).
- Integre controladoras Wi-Fi ao SIEM para acionar playbooks (ex.: isolar cliente comprometido movendo-o para VLAN de quarentena).

## Seção 7 — Qualidade de serviço e desempenho

### 7.1 Métricas essenciais

- **Latência:** tempo ida/volta; monitore `p95`/`p99` e não apenas média.
- **Jitter:** variação de latência entre pacotes; crítico para voz/vídeo.
- **Perda de pacotes:** expressa em %; acima de 1% já afeta VoIP.
- **Throughput:** taxa efetiva de dados.

### 7.2 Bufferbloat e controle de fila

Buffers excessivos em roteadores/switches causam latências altas quando os links saturam.

Mitigações:

- **AQM (Active Queue Management):** RED/CoDel/PIE descartam pacotes preventivamente.
- **Shaping:** limita tráfego a taxa definida, alisando bursts.
- **Policing:** descarta tráfego excedente imediatamente (use com cautela).

### 7.3 Classificação e marcação

Use **DSCP** (camada 3) ou **CoS** (camada 2) para classificar tráfego.

| Classe | DSCP | Aplicação | Tratamento |
|--------|------|-----------|------------|
| EF (Expedited Forwarding) | 46 | Voz / control plane crítico | Latência mínima, fila de prioridade (LLQ) |
| AF41/31/21 | 34/26/18 | Vídeo, aplicações interativas | Fila com garantia de banda |
| BE (Best Effort) | 0 | Tráfego padrão | Fila padrão |
| CS1 (Scavenger) | 8 | Backups, atualizações | Pode ser limitado nos horários de produção |

### 7.4 Bandwidth-Delay Product (BDP)

Determina o volume de dados "em voo" necessário para saturar um enlace:

$$BDP = \text{Bandwidth} \times \text{RTT}$$

Exemplo: link de 100 Mbps com RTT 60 ms ⇒ $BDP = 7{,}5$ MB. Ajustar janelas TCP ou buffers a esse valor evita subutilização.

!!! tip
	Em troubleshooting de performance, siga a sequência: físico → L2 (erros CRC, duplex) → L3 (perda/latência) → L4 (retransmissões) → aplicação. Ferramentas como `iperf3`, `speedtest-cli` e medições SNMP/NetFlow ajudam a quantificar gargalos.

## Seção 8 — Serviços auxiliares de rede

### 8.1 DHCP

- Processo **DORA** (Discover → Offer → Request → Acknowledge) distribui configurações IP automaticamente.
- **Reservas** garantem IPs fixos para servidores mas mantêm gestão centralizada.
- **DHCP Relay (IP Helper):** encaminha broadcasts para servidores DHCP centralizados; essencial em redes roteadas.
- Habilite **DHCP Snooping** para bloquear ofertas falsas e gerar tabela confiável para DAI.

### 8.2 NTP

- Sincroniza relógios; dependência crítica para logs, Kerberos, TLS, assinaturas.
- Use hierarquia **Stratum** (relógio atômico/GPS → servidores internos → clientes).
- Reforce NTP com `ntp auth` (chaves) ou redes dedicadas para evitar spoofing.

### 8.3 SNMP e Telemetria

- Prefira **SNMPv3** (autenticação + criptografia) para coletar métricas.
- MIBs relevantes: `ifTable` (interfaces), `ipNetToMedia` (ARP), `hrStorage`.
- Complementar com **Streaming Telemetry** (gNMI, model-driven) para granularidade fina.

### 8.4 Syslog centralizado

- Encaminhe logs de roteadores, firewalls, controladoras Wi-Fi para coletor central.
- Classifique por severidade (`debug`, `info`, `warning`, `critical`) e aplique retenção compatível com requisitos legais.
- Integre Syslog → SIEM para correlação, enriquecendo com contexto (IPAM, inventário).

### 8.5 IPAM/CMDB

- IPAM (IP Address Management) mantém controle de sub-redes, reservas, estado (livre/usado).
- Integrações úteis: automação (Ansible/Terraform) consome dados do IPAM, evitando colisões.
- Ferramentas populares: NetBox (open source), Infoblox, phpIPAM.

!!! tip
	Configure alertas no IPAM para detectar blocos quase esgotados e automatize abertura de tickets para expansão (ex.: integração NetBox ↔ ServiceNow).

## Seção 9 — Segurança de rede e Zero Trust

### 9.1 Controles em camadas

- **Firewalls L3/L4 (stateful):** controlam tráfego por IP/porta; base para segmentação macro.
- **NGFW / L7:** inspeção de aplicação, identificação de usuários, políticas baseadas em identidade.
- **WAF:** especializado em HTTP/HTTPS (SQL injection, XSS, RCE).
- **IDS/IPS:** Snort/Suricata, Zeek; detectam assinaturas e comportamentos.
- **NDR:** monitora tráfego leste-oeste, detecta beaconing e movimento lateral.

### 9.2 Zero Trust aplicado à rede

Princípios:

1. **Verifique explicitamente:** autentique e autorize cada conexão (usuário, dispositivo, workload).
2. **Aplique privilégio mínimo:** segmentação micro (VLAN, VRF, SDN) e políticas específicas (SGT, NSG).
3. **Assuma violação:** monitore continuamente e automatize respostas.

Implementações práticas:

- **Microsegmentação:** NSX, ACI e firewalls distribuídos aplicam políticas L3/L4/L7 por workload.
- **SD-WAN + SASE:** controles distribuídos na borda, inspeção consistente para usuários remotos.
- **ZTNA:** substitui VPN tradicional por acesso baseado em identidade/contexto.

### 9.3 VPNs e acesso seguro

| Tipo | Tecnologias | Características | Quando usar |
|------|-------------|-----------------|-------------|
| IPsec (IKEv2/ESP) | Túnel site-to-site ou cliente | Alta compatibilidade, suporta hardware offload | Conectar filiais, backhaul para data center |
| SSL VPN | OpenVPN, AnyConnect | Opera sobre TLS/HTTPS, fácil atravessar NAT | Usuários remotos, BYOD |
| WireGuard | Curve25519 + ChaCha20, moderno | Configuração simples, alto desempenho | Ambientes DevOps, túnel overlay rápido |

### 9.4 Ameaças comuns e respostas

- **DoS/DDoS:** tratar com ACLs na borda, scrubbing center, anycast, rate-limit.
- **Spoofing/Man-in-the-Middle:** proteger ARP (DAI), DHCP (snooping), usar 802.1X/mTLS.
- **Ataques de credenciais:** MFA em VPNs, monitoramento de logins anômalos (Azure AD Sign-In logs, Duo, Okta).
- **Lateral Movement:** segmentação, bloqueio de SMB/RDP fora de escopo, honeypots internos.

!!! warning
	Revisite periodicamente regras de firewall/ACL. Ambientes com "permit any" temporários esquecidos são terreno fértil para ataques. Automatize revisões (Infrastructure as Code + CI) para detectar deriva.

## Seção 10 — Nuvem e conexões híbridas

Nesta etapa queremos apenas entender como as mesmas ideias de redes aparecem quando usamos AWS, Azure ou GCP. Kubernetes e redes de contêineres ficarão para aulas futuras.

### 10.1 Componentes principais

| Função | Nome comum | Explicação simples |
|--------|------------|-------------------|
| Rede virtual | VPC (AWS/GCP) / VNet (Azure) | Conjunto de sub-redes isoladas dentro da nuvem |
| Controle estadoful | Security Group / NSG | Lista que diz “quem pode falar com esta máquina” |
| ACL de sub-rede | NACL / firewall de sub-rede | Regras rápidas aplicadas na borda da sub-rede |
| Saída para Internet | Internet Gateway / NAT Gateway | Porta de entrada/saída para tráfego público |
| Observabilidade | Flow Logs | Registro de quem falou com quem (útil para o SIEM) |

### 10.2 Controle de acesso em linguagem direta

- Use **Security Groups/NSG** como se fossem mini firewalls ligados a cada servidor.
- Agrupe regras por função (web, banco, bastion) e dê nomes descritivos.
- Evite `0.0.0.0/0` aberto; sempre restrinja a origem (ex.: IP corporativo, VPN).
- Registre mudanças em repositório Git ou ferramenta de IaC para saber quem alterou o quê.

### 10.3 Ligando nuvem e rede local

- **VPN site-to-site:** túnel IPsec entre o data center e a nuvem. Simples e suficiente para a maioria dos laboratórios.
- **Links dedicados (Direct Connect / ExpressRoute / Interconnect):** usados quando latência e largura de banda precisam ser garantidas.
- **Bastion/Jump Box:** servidor exposto apenas para administração; de lá acessamos os demais recursos internos.

!!! tip
	Antes de criar túneis, confirme que os blocos IP em cada lado não se sobrepõem. Conflitos de CIDR geram rotas estranhas e horas de troubleshooting desnecessário.

## Seção 11 — Arquiteturas de referência e troubleshooting

### 11.1 Topologias modernas

| Topologia | Vantagens | Desafios | Uso típico |
|-----------|-----------|----------|-----------|
| Hub-and-Spoke | Controle centralizado, fácil aplicar políticas | Hub pode virar gargalo; latência extra | Filiais ↔ data center |
| Full Mesh | Latência mínima, redundância máxima | Escala mal (n*(n-1)/2 links) | Backbone de provedores, SD-WAN pequena |
| Leaf-Spine | Dois saltos previsíveis, fácil escalar horizontalmente | Requer cabling estruturado e switches com high throughput | Data centers modernos |
| Anycast | Resiliência, baixa latência global | Necessita BGP e observabilidade distribuída | DNS/CDN, serviços edge |

### 11.2 Metodologia de troubleshooting

1. **Delimite o escopo:** qual camada está falhando? física, lógica, aplicação?
2. **Colete evidências:** `show interface`, `ping`, `traceroute`, captura `pcap`, métricas SNMP.
3. **Teste hipóteses rapidamente:** alterne entre top-down (aplicação → físico) e bottom-up conforme sintomas.
4. **Documente e automatize:** registre causa raiz, ação corretiva e lições aprendidas.

Ferramentas úteis:

- **Wireshark/tshark:** análise de pacotes (ex.: handshake TLS, DNS).
- **NetFlow/IPFIX:** quem está conversando com quem e quanto.
- **Path MTU Discovery / `tracepath`:** identificar fragmentação/ICMP bloqueado.

### 11.3 Runbooks e playbooks

- Crie runbooks versionados para incidentes recorrentes (ex.: "Latência alta entre filiais"; "BGP flap"), com passos e responsáveis.
- Playbooks automatizados (SOAR) podem executar testes básicos (ping, coleta de logs) assim que alertas forem disparados.

!!! tip
	Reforce a cultura de "post-mortem" sem culpados: cada incidente deve gerar ações preventivas (monitoria extra, automação, revisão de arquitetura). Isso fortalece o ciclo de melhoria contínua descrito em `fundamentos.md` (Seção 5 — Princípios de Segurança).

## Glossário essencial (complementar)

- **Anycast:** técnica em que múltiplos servidores anunciam o mesmo prefixo IP via BGP; usuários são atendidos pelo nó mais próximo.
- **BFD (Bidirectional Forwarding Detection):** protocolo leve para detectar falhas em links/adjacências em milissegundos.
- **BDP (Bandwidth-Delay Product):** quantidade de dados "em voo" necessária para saturar um enlace. Fórmula: $BDP = BW \times RTT$.
- **DAI (Dynamic ARP Inspection):** recurso que valida ARP usando base DHCP Snooping, prevenindo spoofing.
- **DSCP (Differentiated Services Code Point):** campo de 6 bits no cabeçalho IP usado para priorizar tráfego.
- **DoH / DoT:** DNS over HTTPS / DNS over TLS — protocolos que criptografam consultas DNS.
- **ECMP:** Equal-Cost Multi-Path; permite múltiplos next-hops com mesmo custo.
- **MSTP/RSTP:** evoluções do Spanning Tree para convergência rápida e múltiplas instâncias.
- **Security Group / NSG:** firewall estadoful associado a máquinas na nuvem; define quem pode acessar determinado recurso.
- **Port Security:** mecanismo em switches que limita MACs por porta e bloqueia dispositivos não autorizados.
- **RPKI/ROA:** infraestrutura de chaves públicas para validar anúncios BGP (Route Origin Authorization).
- **SLAAC:** Stateless Address Autoconfiguration — método para hosts IPv6 derivarem endereços automaticamente usando anúncios de roteador.
- **ZTNA (Zero Trust Network Access):** acesso baseado em identidade/contexto que substitui VPN tradicional sempre aberta.

## Referências

- Peterson, Larry; Davie, Bruce. *Computer Networks: A Systems Approach*.
- [RFC 4632 — Classless Inter-domain Routing (CIDR)](https://datatracker.ietf.org/doc/html/rfc4632)
- [RFC 4861 — Neighbor Discovery for IPv6](https://datatracker.ietf.org/doc/html/rfc4861)
- [RFC 7454 — BGP Operations and Security](https://datatracker.ietf.org/doc/html/rfc7454)
- [RFC 1035 — Domain Names: Implementation and Specification](https://datatracker.ietf.org/doc/html/rfc1035)
- [Lockheed Martin — Cyber Kill Chain](https://www.lockheedmartin.com/en-us/capabilities/cyber/cyber-kill-chain.html)
- [MITRE ATT&CK®](https://attack.mitre.org/) e [MITRE D3FEND](https://d3fend.mitre.org/)
- [NIST SP 800-61 Rev. 2 — Computer Security Incident Handling Guide](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)
- [CIS Controls v8](https://www.cisecurity.org/controls/)
- Documentação oficial: AWS, Azure, GCP, WireGuard, Wi-Fi Alliance.
