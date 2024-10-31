# Introdução ao SET

O Social-Engineer Toolkit (SET) é uma ferramenta de código aberto projetada para simular diversos tipos de ataques de engenharia social. Desenvolvido por David Kennedy, o SET é amplamente utilizado por profissionais de segurança para testar a resiliência de sistemas e usuários contra ameaças de engenharia social.

---

## Instalação no Ubuntu

### Pré-requisitos
- **Python 3**: O SET é escrito em Python.
- **Git**: Para clonar o repositório oficial.
- **Dependências**: Módulos Python necessários.

### Passos de Instalação

#### 1. Atualize o sistema

```bash
sudo apt update && sudo apt upgrade -y
```

#### 2. Instale o Git e Python

```bash
sudo apt install git python3 python3-pip -y
```

#### 3. Clone o repositório do SET

```bash
git clone https://github.com/trustedsec/social-engineer-toolkit.git
```

#### 4. Navegue até o diretório e instale

```bash
cd social-engineer-toolkit
sudo python3 setup.py install
```

#### 5. Execute o SET

```bash
sudo setoolkit
```

---

## Visão Geral dos Menus

### Menu Principal

Ao iniciar o SET, você será apresentado ao seguinte menu principal:

```sql
   1) Social-Engineering Attacks
   2) Penetration Testing (Fast-Track)
   3) Third Party Modules
   4) Update the Social-Engineer Toolkit
   5) Update SET configuration
   6) Help, Credits, and About
```

### Menu 1: Social-Engineering Attacks

Dentro da opção **1) Social-Engineering Attacks**, encontramos:

```mathematica
   1) Spear-Phishing Attack Vectors
   2) Website Attack Vectors
   3) Infectious Media Generator
   4) Create a Payload and Listener
   5) Mass Mailer Attack
   6) Arduino-Based Attack Vector
   7) Wireless Access Point Attack Vector
   8) QRCode Generator Attack Vector
   9) Powershell Attack Vectors
  10) Third Party Modules
```

### Menu 2: Penetration Testing (Fast-Track)

Na opção **2) Penetration Testing (Fast-Track)**, as seguintes opções estão disponíveis:

```sql
   1) Microsoft SQL Bruter
   2) Custom Exploits
   3) SCCM Attack Vector
   4) Dell DRAC/Chassis Default Checker
   5) RID_ENUM - User Enumeration Attack
   6) PSEXEC Powershell Injection
```

---

## Detalhamento das Opções

### Opções do Menu Principal

#### 1) Social-Engineering Attacks

Esta opção leva ao conjunto principal de ferramentas para simular ataques de engenharia social. Aqui, você pode explorar diversos vetores de ataque para avaliar a segurança humana e técnica.

#### 2) Penetration Testing (Fast-Track)

Oferece ferramentas para testes de penetração rápidos, focados em vulnerabilidades conhecidas e exploits customizados.

#### 3) Third Party Modules

Permite a integração de módulos desenvolvidos por terceiros, expandindo as capacidades do SET com funcionalidades adicionais.

#### 4) Update the Social-Engineer Toolkit

Atualiza o SET para a versão mais recente, garantindo acesso às últimas funcionalidades e correções de segurança.

#### 5) Update SET configuration

Permite alterar as configurações do SET, ajustando parâmetros conforme necessário para diferentes cenários de teste.

#### 6) Help, Credits, and About

Fornece informações de ajuda, créditos aos desenvolvedores e detalhes sobre a versão atual do SET.

---

### Opções do Menu 1

#### 1) Spear-Phishing Attack Vectors

Facilita a criação de e-mails de phishing direcionados, permitindo o envio de mensagens personalizadas para alvos específicos. Este módulo ajuda a simular ataques de spear-phishing para testar a suscetibilidade dos usuários.

#### 2) Website Attack Vectors

Permite clonar sites legítimos ou criar páginas de login falsas para capturar credenciais. Inclui técnicas como:

- **Java Applet Attack Method**: Utiliza applets Java para executar código malicioso.
- **Metasploit Browser Exploit Method**: Integra-se com o Metasploit para explorar vulnerabilidades em navegadores.
- **Credential Harvester Attack Method**: Captura credenciais inseridas em páginas clonadas.
- **Tabnabbing Attack Method**: Troca silenciosamente a aba inativa do navegador por uma página de login falsa.

#### 3) Infectious Media Generator

Gera mídias infectadas (como pendrives ou CDs) contendo payloads maliciosos. Quando inseridos em um sistema, podem conceder acesso ao atacante.

#### 4) Create a Payload and Listener

Cria payloads personalizados e configura um listener para estabelecer conexões com sistemas comprometidos. Isso é útil para testar a detecção de malware e comunicações não autorizadas.

#### 5) Mass Mailer Attack

Envia e-mails em massa para uma lista de destinatários, simulando campanhas de phishing em larga escala. Permite personalizar o conteúdo e o remetente das mensagens.

#### 6) Arduino-Based Attack Vector

Utiliza placas Arduino para executar scripts de ataque quando conectadas a um sistema. Pode simular ações de teclado para automatizar a execução de comandos maliciosos.

#### 7) Wireless Access Point Attack Vector

Cria um ponto de acesso sem fio falso para capturar tráfego de rede e credenciais. Isso pode ser usado para testar a segurança de redes Wi-Fi e a consciência dos usuários sobre redes confiáveis.

#### 8) QRCode Generator Attack Vector

Gera códigos QR maliciosos que, quando escaneados, podem redirecionar usuários para sites falsos ou iniciar downloads de malware. Útil para avaliar a segurança em dispositivos móveis.

#### 9) Powershell Attack Vectors

Explora o PowerShell em sistemas Windows para executar comandos maliciosos. Inclui técnicas como:

- **Powershell Alphanumeric Shellcode Injector**: Injeta shellcode usando scripts PowerShell.
- **Powershell Reverse Shell**: Estabelece uma conexão reversa para o atacante.

#### 10) Third Party Modules

Permite adicionar módulos externos ao SET, ampliando as funcionalidades e adaptando-se a necessidades específicas de testes.

---

### Opções do Menu 2

#### 1) Microsoft SQL Bruter

Realiza ataques de força bruta em servidores Microsoft SQL para descobrir credenciais fracas. Auxilia na identificação de bancos de dados vulneráveis.

#### 2) Custom Exploits

Permite a execução de exploits personalizados. O usuário pode inserir código exploit específico para testar vulnerabilidades não cobertas pelos módulos padrão.

#### 3) SCCM Attack Vector

Explora vulnerabilidades no Microsoft System Center Configuration Manager (SCCM) para ganhar acesso não autorizado.

#### 4) Dell DRAC/Chassis Default Checker

Verifica dispositivos Dell DRAC (Dell Remote Access Controller) em busca de credenciais padrão não alteradas, identificando potenciais pontos de entrada.

#### 5) RID_ENUM - User Enumeration Attack

Utiliza técnicas de enumeração de IDs relativos (RIDs) para descobrir nomes de usuários em sistemas Windows, ajudando a mapear possíveis alvos.

#### 6) PSEXEC Powershell Injection

Explora o utilitário PsExec para executar comandos PowerShell remotamente em sistemas Windows, permitindo a execução de código sem arquivos.

---

## Considerações Éticas e Legais

- **Uso Responsável**: O SET deve ser utilizado apenas para fins educacionais, acadêmicos ou em ambientes de teste com autorização explícita.
- **Consentimento**: Nunca execute testes em sistemas ou redes sem permissão por escrito dos proprietários.
- **Conformidade Legal**: Esteja ciente das leis e regulamentações locais e internacionais relacionadas a atividades de teste de penetração e segurança da informação.
- **Ética Profissional**: Como profissionais de segurança, é nossa responsabilidade proteger informações e sistemas, não comprometê-los.

---

## Conclusão

O Social-Engineer Toolkit é uma ferramenta abrangente que permite simular uma variedade de ataques de engenharia social. Compreender suas funcionalidades ajuda a identificar vulnerabilidades e fortalecer defesas contra ameaças reais. Lembre-se sempre de utilizar essas ferramentas de forma ética e responsável, contribuindo para um ambiente digital mais seguro.

---

