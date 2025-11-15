Arquitetura de Cloud em Redes e Segurança (Aula 02 — Tópicos Avançados)
Dentro do nosso objetivo de construir uma base sólida em redes e entender onde aplicar controles de segurança, a Arquitetura de Cloud (IaaS) representa a próxima fronteira. Ela pega os fundamentos que discutimos — como endereçamento, roteamento e segmentação — e os aplica em um ambiente virtualizado e altamente dinâmico, introduzindo novos conceitos e controles essenciais para a cibersegurança.
________________________________________
O Ambiente Virtual da Nuvem
Quando migramos para a Nuvem, o primeiro conceito essencial é a Rede Privada Virtual (VPC) ou VNet. Pense na VPC como o seu data center virtual isolado na Nuvem. Dentro desta VPC, criamos Sub-redes que, assim como no mundo físico com as VLANs (802.1Q), são subconjuntos lógicos da rede principal, definidos por blocos CIDR/máscaras (L3) para organizar e segmentar recursos. Cada Sub-rede é associada a uma Tabela de Rotas (Route Table) que dita como o tráfego deve ser encaminhado, se localmente (dentro da VPC), para a Internet, ou para outras redes.
A conexão da sua VPC com a Internet é controlada por Internet Gateways (para entrada e saída de tráfego público) e NAT Gateways (para permitir que instâncias em sub-redes privadas acessem a Internet sem terem um IP público).
________________________________________
Controles de Segurança e Segmentação
Na Cloud IaaS, a segurança de rede é implementada em múltiplas camadas virtuais:
1.	Network Access Control Lists (NACLs): Estes operam no nível da sub-rede e são stateless (sem estado). Isso significa que, se você permitir o tráfego de entrada (inbound) para uma porta, você deve explicitamente permitir o tráfego de retorno de saída (outbound) na mesma porta. Eles funcionam como um primeiro filtro, antes do tráfego chegar à instância.
2.	Security Groups (SGs): Estes operam no nível da instância (interface de rede virtual) e são stateful (com estado). Eles lembram o tráfego que foi iniciado. Se uma regra permitir o tráfego de entrada, o tráfego de retorno correspondente é automaticamente permitido, simplificando muito a gestão. Em termos de segurança, eles funcionam como Firewalls distribuídos L3/L4 (stateful) aplicados diretamente ao ativo. O uso de Security Groups juntamente com NACLs oferece uma defesa em profundidade (defense-in-depth), onde a NACL atua na borda da sub-rede e o Security Group protege o recurso final.
Em uma arquitetura moderna e segura, a segmentação (separar redes por função ou sensibilidade) é vital, reforçando o conceito de Zero Trust na Nuvem.
________________________________________
Conexões Avançadas e Híbridas
Para conectar múltiplas VPCs (seja dentro da mesma conta ou para terceiros), usamos o Peering de VPCs. Já o Transit Gateway é uma evolução, agindo como um hub central de roteamento, conectando dezenas ou centenas de VPCs e redes on-premise de forma escalável, eliminando a complexidade de gerenciar conexões full-mesh (todos com todos).
A ponte entre a nuvem e o seu data center físico (on-premise) é feita através de serviços como Direct Connect ou ExpressRoute, que fornecem uma conexão privada e dedicada (sem passar pela Internet pública) e de alta largura de banda, sendo cruciais para cenários de Arquitetura Híbrida.
________________________________________
Redes em Contêineres (Kubernetes)
Em plataformas de contêineres, como o Kubernetes, a rede é abstraída pela interface CNI (Container Network Interface).
•	Cada Pod recebe seu próprio IP (geralmente de uma faixa diferente dos IPs do Node que o hospeda), permitindo que sejam endereçáveis e comunicáveis como se fossem máquinas virtuais.
•	Um Service atua como um Balanceador de Carga virtual (L4 ou L7) para um conjunto de Pods, provendo um IP estável e DNS interno para acesso (tipos ClusterIP, NodePort, LoadBalancer).
•	Para impor os controles de segurança L3/L4 entre os Pods ou Namespaces (segmentação), utilizamos as Network Policies. Elas definem quais Pods podem se comunicar com quais, baseadas em labels ou endereços, sendo a ferramenta primária de segurança de rede microsegmentada dentro do Kubernetes.
Em resumo, a arquitetura de Nuvem virtualiza e distribui os controles de rede — Endereçamento, Roteamento, Switching e Segurança. Como profissionais de Cibersegurança, nosso papel é entender como esses componentes virtuais (VPC, Sub-redes, Route Tables, SGs, NACLs) se mapeiam para os modelos de referência (OSI/TCP-IP) e garantir que as Políticas de Acesso e a Segmentação estejam corretamente configuradas para mitigar as ameaças de rede (como spoofing, DoS ou acesso não autorizado).
________________________________________
