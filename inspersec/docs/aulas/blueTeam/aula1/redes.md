# OSI Model

O **Open Systems Interconnection Model (OSI)** é um conceito desenvolvido pela **International Organization for Standardization (ISO)** que explica como as comunicações em uma rede de computadores deveriam ocorrer, e ele nos explica como as trocas de informações ocorrem na internet, dividindo-se em **7 camadas**:

1. Physical Layer
2. Data Link Layer
3. Network Layer
4. Transport Layer
5. Session Layer
6. Presentation Layer
7. Application Layer

## 1. Physical Layer

A **Physical Layer (Camada Física)** é a camada mais baixa do modelo OSI e representa a transferência física dos dados entre dispositivos.

Nessa camada não existe preocupação com o significado da informação que está sendo transmitida. Os dados são simplesmente representados como **bits (0 e 1)** e enviados através de algum meio físico.

Na vida real, essa camada é representada pelos componentes responsáveis por transportar fisicamente os sinais, como:

* cabos Ethernet;
* fibra óptica;
* conectores;
* sinais elétricos;
* ondas de rádio utilizadas pelo Wi-Fi;
* interfaces físicas de equipamentos de rede.

Por exemplo, quando um computador envia informações através de um cabo Ethernet, a Physical Layer é responsável pela transmissão dos bits através de sinais elétricos pelo cabo.

## 2. Data Link Layer

A **Data Link Layer (Camada de Enlace)** é responsável pela comunicação entre dispositivos que estão conectados diretamente ou fazem parte da mesma rede local.

Enquanto a camada física simplesmente transmite bits, a Data Link Layer começa a organizar esses dados em estruturas chamadas **frames**.

É nessa camada que encontramos os **endereços MAC (Media Access Control)**, utilizados para identificar interfaces de rede dentro de uma rede local.

Na vida real, essa camada pode ser representada pela comunicação entre seu computador e um switch dentro de uma rede Ethernet.

Um exemplo seria um computador enviando informações para outro computador conectado ao mesmo switch. O switch utiliza os **endereços MAC** para descobrir para qual dispositivo aquele frame deve ser encaminhado.

Alguns exemplos relacionados a essa camada são:

* Ethernet;
* endereços MAC;
* frames;
* switches;
* VLANs.

## 3. Network Layer

A **Network Layer (Camada de Rede)** é responsável por permitir a comunicação entre dispositivos que estão em **redes diferentes**.

É nessa camada que encontramos o **IP (Internet Protocol)** e os endereços IP utilizados para identificar dispositivos e redes.

Enquanto a Data Link Layer trabalha principalmente com a comunicação dentro de uma rede local utilizando endereços MAC, a Network Layer permite que os dados sejam encaminhados entre diferentes redes.

Na vida real, essa camada é representada principalmente pelos **roteadores**, que analisam os endereços IP e decidem qual caminho os pacotes devem seguir para chegar ao destino.

Por exemplo, quando você acessa um servidor que está em outro país, os pacotes precisam atravessar diversos roteadores e redes diferentes até chegar ao servidor.

Alguns exemplos relacionados a essa camada são:

* IPv4;
* IPv6;
* endereços IP;
* pacotes;
* roteadores;
* ICMP.

## 4. Transport Layer

A **Transport Layer (Camada de Transporte)** é responsável pela comunicação entre aplicações executadas nos dispositivos.

É nessa camada que encontramos principalmente os protocolos **TCP (Transmission Control Protocol)** e **UDP (User Datagram Protocol)**.

O TCP fornece uma comunicação confiável, verificando se os dados chegaram corretamente ao destino e realizando retransmissões caso alguma informação seja perdida.

O UDP possui menos mecanismos de controle e simplesmente envia os dados sem garantir que eles chegaram ao destino, permitindo uma comunicação mais simples e rápida.

Também é nessa camada que encontramos o conceito de **portas**, utilizadas para identificar diferentes serviços e aplicações dentro de um dispositivo.

Por exemplo:

* HTTP normalmente utiliza a porta 80;
* HTTPS normalmente utiliza a porta 443;
* SSH normalmente utiliza a porta 22;
* DNS normalmente utiliza a porta 53.

Na vida real, podemos imaginar o endereço IP identificando um computador e a porta identificando qual aplicação dentro daquele computador deve receber os dados.

## 5. Session Layer

A **Session Layer (Camada de Sessão)** é responsável pelo estabelecimento, gerenciamento e encerramento de sessões de comunicação entre aplicações.

Uma sessão representa uma comunicação mantida entre dois sistemas durante determinado período.

Essa camada ajuda a controlar quando uma comunicação começa, por quanto tempo ela permanece ativa e quando deve ser encerrada.

Na vida real, podemos pensar em uma aplicação mantendo uma sessão com um servidor enquanto o usuário utiliza determinado serviço.

Por exemplo, durante uma comunicação entre uma aplicação cliente e um servidor, mecanismos de sessão podem permitir que aquela comunicação seja mantida e controlada durante diferentes trocas de informações.

A Session Layer é uma camada que normalmente não aparece de forma tão claramente separada nas implementações modernas de redes, já que várias de suas funções podem ser implementadas diretamente pelas aplicações ou por outros protocolos.

## 6. Presentation Layer

A **Presentation Layer (Camada de Apresentação)** é responsável pela forma como os dados são representados para que diferentes sistemas consigam interpretar as informações corretamente.

Ela pode realizar tarefas relacionadas à:

* codificação;
* decodificação;
* serialização;
* compressão;
* criptografia.

Por exemplo, dois computadores podem possuir sistemas completamente diferentes, mas precisam concordar sobre como determinada informação será representada durante a comunicação.

Na vida real, podemos encontrar conceitos relacionados a essa camada em formatos e codificações como:

* UTF-8;
* JSON;
* XML;
* JPEG;
* PNG.

Criptografia utilizada durante determinadas comunicações também é frequentemente associada à Presentation Layer quando estamos explicando o modelo OSI de forma conceitual.

Portanto, essa camada basicamente garante que os dados estejam em um formato que possa ser interpretado corretamente pela aplicação que irá recebê-los.

## 7. Application Layer

A **Application Layer (Camada de Aplicação)** é a camada mais próxima das aplicações utilizadas pelo usuário.

Ela representa os protocolos e serviços de rede utilizados pelas aplicações para realizar comunicações através da rede.

É importante entender que a Application Layer não representa necessariamente o programa inteiro, como um navegador, mas os protocolos e serviços de rede utilizados por essas aplicações.

Alguns exemplos são:

* HTTP e HTTPS para comunicação web;
* DNS para resolução de nomes;
* SMTP para envio de e-mails;
* IMAP para acesso a e-mails;
* SSH para acesso remoto.

Na vida real, quando você abre um navegador e acessa um site através de HTTPS, está utilizando um protocolo associado à Application Layer.


# Encapsulation

Ja finalizando 

A **Encapsulation (Encapsulação)** é o processo onde cada camada adiciona suas próprias informações aos dados recebidos da camada superior antes de enviá-los para a camada inferior.

Essas informações normalmente são adicionadas através de **headers (cabeçalhos)** e, em alguns casos, **trailers**, que possuem informações necessárias para que cada protocolo consiga realizar sua função.

Esse conceito é importante porque permite que cada camada se preocupe apenas com sua própria responsabilidade durante a comunicação.

Podemos entender a encapsulação acompanhando uma informação enquanto ela passa pelas diferentes camadas:

### Application Data

Tudo começa na **Application Layer**, onde temos os dados que uma aplicação deseja enviar.

Por exemplo, quando enviamos uma mensagem, realizamos uma pesquisa em um site ou fazemos uma requisição HTTP, inicialmente temos apenas os dados produzidos pela aplicação.

Esses dados são então enviados para a camada abaixo.

### TCP Segment / UDP Datagram

Quando os dados chegam na **Transport Layer**, protocolos como TCP ou UDP adicionam seus próprios headers.

Caso seja utilizado **TCP**, os dados passam a formar um **TCP Segment**.

Caso seja utilizado **UDP**, eles passam a formar um **UDP Datagram**.

Esses headers possuem informações necessárias para o funcionamento do protocolo, como as portas de origem e destino.

Depois disso, o Segment ou Datagram é enviado para a Network Layer.

### IP Packet

Na **Network Layer**, o protocolo IP adiciona seu próprio header aos dados recebidos da Transport Layer.

Esse header possui informações como:

* endereço IP de origem;
* endereço IP de destino.

Após a adição desse header, temos um **IP Packet**, que pode ser encaminhado entre diferentes redes através de roteadores.

O Packet é então enviado para a camada abaixo.

### Data Link Frame

Quando o IP Packet chega na **Data Link Layer**, protocolos como Ethernet ou Wi-Fi adicionam suas próprias informações.

Nessa etapa podem ser adicionados tanto um **header** quanto um **trailer**, formando o que chamamos de **Frame**.

Esse Frame contém as informações necessárias para realizar a comunicação através do enlace atual.

Por fim, o Frame pode ser transmitido pela **Physical Layer** através do meio físico utilizado pela rede.

De forma simplificada, podemos visualizar o processo como:

**Application Data → TCP Segment / UDP Datagram → IP Packet → Ethernet/Wi-Fi Frame → Bits**

Ou seja, conforme os dados descem pelas camadas, cada protocolo adiciona as informações necessárias para realizar sua função.

## Decapsulation

Quando os dados chegam ao dispositivo de destino, ocorre o processo contrário, chamado **Decapsulation (Desencapsulação)**.

Nesse processo, os dados começam nas camadas inferiores e vão subindo até chegar novamente na aplicação.

Cada camada analisa e remove as informações relacionadas ao seu protocolo antes de entregar os dados para a camada superior.

Podemos representar esse processo como:

**Bits → Frame → IP Packet → TCP Segment / UDP Datagram → Application Data**

Por exemplo, a Data Link Layer processa as informações do Frame e entrega o IP Packet para a Network Layer.

A Network Layer processa o header IP e entrega o Segment ou Datagram para a Transport Layer.

A Transport Layer processa suas próprias informações e finalmente entrega os dados para a aplicação correta.

## Exemplo de Encapsulation na prática

Podemos imaginar o processo que acontece quando realizamos uma pesquisa em um site.

Primeiro, escrevemos nossa pesquisa no navegador e pressionamos Enter.

O navegador prepara uma requisição **HTTP/HTTPS** contendo os dados necessários e envia essa informação para a Transport Layer.

Na Transport Layer, o **TCP** estabelece uma conexão com o servidor e adiciona seu próprio header aos dados, formando um TCP Segment.

Esse Segment é enviado para a Network Layer.

Na Network Layer, o protocolo **IP** adiciona informações como o endereço IP de origem e o endereço IP de destino, formando um IP Packet.

Esse Packet é enviado para a Data Link Layer.

Na Data Link Layer, tecnologias como **Ethernet ou Wi-Fi** adicionam o header e trailer necessários, formando um Frame.

O Frame pode então ser transmitido fisicamente pela rede.

Quando esse Frame chega a um roteador, o roteador processa as informações da Data Link Layer e analisa o endereço IP de destino para decidir para onde o Packet deve ser encaminhado.

Durante o caminho até o servidor, diferentes roteadores podem repetir esse processo, encaminhando o IP Packet através de diferentes enlaces.

Quando os dados finalmente chegam ao dispositivo de destino, ocorre a **Decapsulation**, onde as informações adicionadas pelas diferentes camadas são processadas e removidas até que os dados originais sejam entregues para a aplicação.

Portanto, durante uma comunicação, os dados não são simplesmente enviados diretamente de uma aplicação para outra. Eles passam por diferentes camadas, e cada uma adiciona ou processa as informações necessárias para que os dados consigam chegar corretamente ao seu destino.

