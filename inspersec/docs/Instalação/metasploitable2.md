# Instalando o Metasploitable 2 no VirtualBox

O Metasploitable 2 é uma máquina virtual Linux intencionalmente vulnerável, projetada para testes de segurança e aprendizado de ferramentas de invasão como o Metasploit. Neste tutorial, vamos configurar o Metasploitable 2 no Oracle VirtualBox com as configurações ideais.

## Pré-requisitos
- [Oracle VirtualBox](https://www.virtualbox.org/wiki/Downloads) instalado e atualizado.
- [Metasploitable 2](https://sourceforge.net/projects/metasploitable/files/Metasploitable2/) 
- Com o arquivo do Metasploitable 2 baixado (geralmente um arquivo `.zip` contendo um disco virtual `.vmdk`). Extraia esse arquivo em uma pasta acessível.

## Passo a Passo da Criação da Máquina Virtual

### 1. Iniciando uma Nova Máquina
1. Abra o **Oracle VirtualBox Gerenciador**.
2. Clique no botão **Novo** (ícone azul em forma de estrela) para iniciar o assistente de criação.
3. Preencha os campos básicos:
   - **Nome:** Metasploitable2
   - **Tipo:** Linux
   - **Versão:** Ubuntu (32-bit)
4. Clique em **Próximo**.

### 2. Memória RAM
- Aloque ao menos **1024 MB** de memória principal para a VM, garantindo uma inicialização e processamento rápido.
- Clique em **Próximo**.

### 3. Disco Rígido Virtual Embutido
1. Ao invés de criar um disco novo, selecione a opção **"Utilizar um disco rígido virtual existente"**.
2. Clique no ícone de uma pasta com setinha verde à direita.
3. Uma janelinha se abrirá. Clique no botão de **Acrescentar** (ícone de +).
4. Navegue até a pasta onde você extraiu previamente o arquivo `.zip` do Metasploitable.
5. Selecione o arquivo de disco chamado `Metasploitable.vmdk` (geralmente com cerca de 8 GB).
6. Após selecioná-lo e adicioná-lo, escolha-o na lista para garantir que está assinalado.
7. Clique em **Criar** para fechar o assistente.

## Configurações Finais (Assegurando o Pleno Funcionamento)

Antes de iniciar a máquina, há ajustes pontuais importantes (conforme nossos padrões de instalação) que você precisa verificar para que a máquina tenha 100% de funcionamento e segurança.

Com a sua VM `Metasploitable2` selecionada na tela inicial do VirtualBox, clique no botão **Configurações** (ou use as teclas `Ctrl+S`).

### Categoria "Sistema"
1. Navegue para a sub-categoria **Placa-mãe**.
    - Valide que a **Memória Base** está como configurada: `1024 MB`.
    - Na "Ordem de Boot", verifique se o **Disco Rígido** está presente.

### Categoria "Rede" (Muito Importante!)
Como o Metasploitable 2 é uma máquina propositalmente exposta a vários tipos de falhas, é essencial que não configuremos sua rede para ter acesso aberto ou NAT comum. Para atacar adequadamente esta VM junto com a sua VM de ataque (como um Kali Linux).

1. Entre na secão de **Rede**.
2. Ative o **Adaptador 1** (marcando a caixinha "Habilitar Placa de Rede").
3. Altere o campo **Conectado a:** de "NAT" para **Placa de rede exclusiva de hospedeiro (Host-only Adapter)**.
4. No campo **Nome**, selecione a rede apropriada da qual seu adaptador Host-only faz parte (por exemplo: `VirtualBox Host-Only Ethernet Adapter #2`).

> **Importante:** A sua VM atuante na posição de atacante, como o Kali Linux, também deve estar configurada paralelamente no mesmo Adaptador (Host-only) para que ambas tenham comunicação de rede restrita ao seu computador, mas sem saírem para a internet.

### Inicialização e Credenciais
1. Ao revisar as configurações acima, clique no botão de **OK** para aplicar tudo e fechar.
2. Com o seu Metasploitable selecionado e com a máquina marcando o status de "Desligado", clique em **Iniciar** (seta verde).
3. Após o boot do sistema no estilo linha de comando, ele pedirá credenciais.
   O usuário e a senha padrão desta máquina são:
   - **Usuário:** `msfadmin`
   - **Senha:** `msfadmin`

Pronto, a máquina está rodando 100% configurada e no ponto seguro de ataque para os testes práticos!
