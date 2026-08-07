# Linux Básico

## O que é Linux?

O **Linux** é o núcleo que faz um sistema operacional funcionar. Assim como um motor é essencial para um carro, o Linux é o coração que gerencia o hardware do computador.

### Componentes do Linux:

- **Sistema Operacional**: O Linux gerencia todo o hardware, permitindo que o computador funcione de forma eficiente.
- **Kernel (Núcleo)**: O núcleo é a parte principal do sistema que faz a comunicação entre o software e o hardware.
- **Distribuições Linux**: Assim como existem diferentes modelos de carros, o Linux tem várias distribuições (ex.: Ubuntu, Fedora), cada uma adaptada para necessidades e preferências específicas.

---

## O que são Distribuições Linux?

As **Distribuições Linux** são "versões" diferentes do Linux, criadas para atender a diversos tipos de usuários e cenários.

### Por que existem tantas distribuições?

- Cada distribuição é otimizada para diferentes fins, como:
  - **Uso pessoal**
  - **Empresas**
  - **Servidores**
  - **Desenvolvimento**

Todas compartilham o mesmo **kernel**, mas com configurações e ferramentas próprias.

---

## Organização de Arquivos no Linux

No Linux, **tudo é um arquivo**, desde programas até dispositivos. O sistema de arquivos é organizado em uma estrutura hierárquica, partindo do diretório raiz (`/`).

### Diretórios Principais

- **/**: Diretório raiz, o ponto de partida do sistema de arquivos.
- **/bin**: Armazena os comandos essenciais e programas do sistema.
- **/etc**: Contém os arquivos de configuração do sistema e dos programas.
- **/dev**: Representa os dispositivos de hardware como arquivos.
- **/home**: Diretórios pessoais dos usuários, onde estão seus arquivos e configurações.
- **/lib**: Contém as bibliotecas essenciais para o funcionamento dos programas.
- **/tmp**: Usado para armazenar arquivos temporários.
- **/usr**: Armazena programas e bibliotecas adicionais para os usuários.
- **/var**: Armazena arquivos variáveis, como logs e dados temporários.

---

## Caminho Absoluto vs. Relativo

- **Caminho Absoluto**: Começa sempre com o diretório raiz (`/`) e define o caminho completo até o destino.
  - Exemplo: `/etc/X11/xinit`
- **Caminho Relativo**: Baseia-se no diretório atual.
  - Exemplo: `X11/xinit` (se você já estiver no diretório `/etc`)

---

## Comandos Básicos no Linux

### Navegação:

- **cd [diretório]**: Muda para o diretório especificado.
- **ls**: Lista os arquivos e diretórios no diretório atual.
- **pwd**: Exibe o caminho absoluto do diretório atual.

### Manipulação de Arquivos:

- **cp [origem] [destino]**: Copia arquivos ou diretórios.
- **mv [origem] [destino]**: Move ou renomeia arquivos.
- **rm [arquivo]**: Remove arquivos (use `rm -r` para remover diretórios).
- **mkdir [nome_do_diretório]**: Cria um novo diretório.

### Informações do Sistema:

- **whoami**: Mostra o nome do usuário logado.
- **df -h**: Exibe o uso do disco de forma legível para humanos.
- **cat [arquivo]**: Exibe o conteúdo de um arquivo.
- **more** / **less**: Permitem visualizar arquivos longos de maneira paginada.

---

## Gerenciamento de Pacotes e Permissões no Linux

### Comando **sudo**:

O comando **sudo** permite que um usuário execute comandos com privilégios de administrador (root).

- **Sintaxe**: `sudo [comando]`
- **Exemplo**: `sudo apt update`

No Linux, tarefas que afetam todo o sistema, como instalar ou remover pacotes, requerem privilégios de administrador.

---

## Gerenciamento de Pacotes com **apt**

O **apt** é o gerenciador de pacotes usado em distribuições baseadas no Debian, como o Ubuntu.

- **Atualizar Lista de Pacotes**: 
  ```bash
  sudo apt update
    ```
---

## Instalar Pacotes

Para instalar pacotes no Linux usando o gerenciador de pacotes `apt`, utilize o seguinte comando:

```bash
sudo apt install [nome_do_pacote]
```

---

## Remover Pacotes

Para remover pacotes no Linux usando o gerenciador de pacotes `apt`, utilize o seguinte comando:

```bash
sudo apt remove [nome_do_pacote]
```

---

## Comando `curl`

O `curl` é uma ferramenta de linha de comando extremamente útil para transferir dados de ou para um servidor. Ele suporta diversos protocolos, como HTTP, HTTPS, FTP, entre outros.

### Funções do `curl`

- **Download de arquivos**: Baixar arquivos de um servidor diretamente pelo terminal.
- **Envio de dados**: Enviar dados para servidores, comumente usado para fazer requisições POST.
- **Testes de API**: Fazer requisições GET, POST, PUT e DELETE para APIs RESTful.

### Sintaxe Básica

A sintaxe do `curl` segue o seguinte formato:

```bash
curl [opções] [URL]
```

- **Exemplo**: `curl -O https://exemplo.com/arquivo.txt`


### Opções Comuns

- **-O**: Salva o arquivo com o mesmo nome do arquivo remoto.
- **-o [nome_do_arquivo]**: Salva o arquivo com um nome específico.
- **-X [método]**: Especifica o método HTTP (GET, POST, PUT, DELETE).
- **-d [dados]**: Envia dados para o servidor (usado com POST).
- **-H [cabeçalho]**: Adiciona cabeçalhos personalizados à requisição.
- **-i**: Exibe os cabeçalhos da resposta.



---

## Download de Arquivos com o Comando **wget**

O comando **wget** é usado para baixar arquivos da internet diretamente no terminal.

```bash
wget [URL_do_arquivo]
```

---

## Gerenciamento de Pacotes com **dpkg**

O **dpkg** é uma ferramenta de baixo nível para gerenciamento de pacotes **.deb**.


```bash
sudo dpkg -i [nome_do_pacote.deb]
```

---

## Diferenças entre **apt** e **dpkg**

| **apt**                                      | **dpkg**                                         |
|----------------------------------------------|--------------------------------------------------|
| Interface de mais alto nível                 | Interface de baixo nível                         |
| Gerencia automaticamente dependências        | Não gerencia dependências automaticamente        |
| Usa repositórios online para buscar pacotes  | Gerencia pacotes `.deb` localmente               |
| Suporta operações como atualização e upgrade | Focado apenas na instalação, remoção e consulta  |
| Verifica e resolve dependências automaticamente | Não resolve dependências, exigindo configuração manual |
| Adequado para uso cotidiano de usuários       | Adequado para uso avançado e administração manual |
| Pode remover pacotes e dependências associadas automaticamente | Remove apenas pacotes sem considerar dependências |
| Integração com outras ferramentas de repositório | Funciona de forma isolada, sem busca online      |

---

## Editores de Texto no Linux

### Vim

- **Função**: Editor de texto poderoso com muitos recursos avançados.
- **Instalação**:

```bash
sudo apt install vim
```

- **Comandos Básicos**:

  - **Modo Normal**: Pressione `i` para entrar no modo de edição.
  - **Modo de Edição**: Pressione `Esc` para voltar ao modo normal.
  - **Salvar e Sair**: No modo normal, digite `:wq` e pressione `Enter`.

---

### Nano

- **Função**: Editor de texto simples e fácil de usar.
- **Instalação**:

```bash
sudo apt install nano
```

- **Comandos Básicos**:

  - **Salvar e Sair**: Pressione `Ctrl` + `O` para salvar e `Ctrl` + `X` para sair.

--- 

### Gedit

- **Função**: Editor de texto com interface gráfica.
- **Instalação**:

```bash
sudo apt install gedit
```

- **Comandos Básicos**:

  - **Abrir Arquivo**: Execute `gedit [arquivo]` para abrir um arquivo específico.

---

## Alias

Os **aliases** são atalhos que permitem criar comandos personalizados no terminal.

### Criar um Alias

Para criar um **alias** voce pode adicionar o comando no arquivo `.bashrc` que fica no diretório `home` do usuário.
Você pode criar um arquivo separado para os aliases, como `.alias`, e adicionar a seguinte linha no `.bashrc`:

```bash	
  ~/.alias
```

Com isso, você pode adicionar seus aliases no arquivo `.alias` e eles serão carregados toda vez que você abrir um terminal.

**Sinatxe para criar um alias:**

```bash
alias [nome_do_alias]='[comando]'
```
