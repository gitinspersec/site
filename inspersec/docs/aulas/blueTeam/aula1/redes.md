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

Já finalizando, é importante mencionar o conceito de **encapsulação**, que, no contexto de redes, se refere ao processo de adicionar headers específicos de cada camada à informação, permitindo, assim, que cada camada foque apenas nas suas próprias responsabilidades.

Para entender melhor esse processo, imagine que você está enviando uma informação pela internet. Inicialmente, temos apenas os dados gerados pela aplicação, como uma mensagem ou uma requisição HTTP.

Conforme essa informação vai descendo pelas camadas, cada uma adiciona os dados necessários para realizar a sua função.

Na **Transport Layer**, por exemplo, protocolos como TCP ou UDP adicionam seus próprios headers. Caso seja utilizado TCP, o resultado é chamado de **TCP Segment**; caso seja utilizado UDP, temos um **UDP Datagram**.

Em seguida, essa informação chega à **Network Layer**, onde o protocolo IP adiciona seu próprio header, contendo informações como o endereço IP de origem e o endereço IP de destino. A partir desse momento, temos um **IP Packet**, que pode ser encaminhado entre diferentes redes.

Depois, o Packet chega à **Data Link Layer**, onde protocolos como Ethernet adicionam suas próprias informações, formando um **Frame**. Nessa camada, além de um header, também pode ser adicionado um **trailer**.

Por fim, chegamos à **Physical Layer**, onde toda essa informação é transmitida através do meio físico da rede na forma de bits.

De forma simplificada, podemos visualizar o processo da seguinte maneira:

![Modelo OSI](imagens/what-is-the-osi-model.svg)

**Application Data → TCP Segment / UDP Datagram → IP Packet → Frame → Bits**

Perceba que, conforme os dados descem pelas camadas, cada uma adiciona as informações necessárias para cumprir sua responsabilidade. É justamente esse processo que chamamos de **Encapsulation**.

Quando a informação chega ao dispositivo de destino, acontece o processo contrário, chamado de **Decapsulation (Desencapsulação)**. Nesse caso, cada camada processa e remove as informações correspondentes ao seu protocolo até que os dados originais cheguem à aplicação.

Podemos visualizar esse processo como:

**Bits → Frame → IP Packet → TCP Segment / UDP Datagram → Application Data**

Então, de forma simples, durante a **Encapsulation** os dados vão sendo "empacotados" enquanto descem pelas camadas e, durante a **Decapsulation**, vão sendo "desempacotados" enquanto sobem pelas camadas no dispositivo de destino.
