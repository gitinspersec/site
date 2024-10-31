# Zphisher - Ferramenta de Phishing para Fins Éticos

## Introdução

O **Zphisher** é uma ferramenta de phishing de código aberto que facilita a criação de páginas de login falsas para capturar credenciais de usuários. Embora essa ferramenta seja amplamente utilizada, é importante ressaltar que o uso de ferramentas de phishing é **ilegal e antiético** se feito sem autorização. O uso dessas ferramentas é permitido apenas em um **ambiente controlado**, com **permissão explícita** e para fins **educacionais** ou de **testes de segurança**, como no contexto de **pentesting** (teste de penetração).

Nesta aula, abordaremos como instalar e configurar o Zphisher para fins éticos e educacionais, garantindo que seja utilizado de maneira segura e responsável.

---

## Objetivos da Aula

- Entender o que é o Zphisher e como ele funciona.
- Aprender a instalar e configurar o Zphisher.
- Conhecer os diferentes tipos de ataques que podem ser simulados com a ferramenta.
- Compreender a importância da ética e das permissões ao utilizar ferramentas de phishing.

---

## O que é o Zphisher?

**Zphisher** é uma ferramenta que permite criar páginas de login falsas para simular ataques de phishing. Com ela, é possível emular páginas de login de plataformas conhecidas, como **Facebook**, **Instagram**, **Google**, entre outras. O objetivo é educar os usuários e treinar equipes para identificar tentativas de phishing, tornando-os mais preparados para lidar com ameaças reais.

O Zphisher também é utilizado por profissionais de segurança em **testes de penetração**, ajudando a identificar vulnerabilidades humanas e avaliar a conscientização dos usuários.

> **Nota Importante**: Ferramentas de phishing só devem ser utilizadas com a autorização explícita do proprietário do sistema ou do ambiente a ser testado. O uso indevido pode resultar em sérias consequências legais.

---

## Instalando o Zphisher

Vamos aos passos para instalar o Zphisher em um sistema Linux.

### 1. Instalar o Git

Primeiro, é necessário ter o **Git** instalado no seu sistema. O Git é utilizado para clonar o repositório oficial do Zphisher. Se você estiver usando **Linux**, pode instalar o Git com o seguinte comando:

```bash
sudo apt install git
```

### 2. Clonar o Repositório do Zphisher

Com o Git instalado, clone o repositório do Zphisher executando o comando abaixo:

```bash
git clone https://github.com/htr-tech/zphisher.git
```

### 3. Navegar até o Diretório Clonado

Depois de clonar o repositório, navegue até o diretório do Zphisher:

```bash
cd zphisher
```

### 4. Conceder Permissão de Execução

Antes de executar o script, é necessário conceder permissão de execução ao arquivo principal do Zphisher:

```bash
chmod +x zphisher.sh
```

### 5. Executar o Zphisher

Agora você está pronto para executar o Zphisher:

```bash
./zphisher.sh
```

---

## Usando o Zphisher

### Escolher a Plataforma de Phishing

Quando você iniciar o Zphisher, ele apresentará um menu com uma lista de plataformas para as quais você pode criar páginas de login falsas, como **Facebook**, **Instagram**, **Google**, **PayPal**, entre outras.

Escolha a plataforma que deseja emular digitando o número correspondente e pressionando **Enter**.

### Escolher o Tipo de Ataque

Depois de selecionar a plataforma, o Zphisher oferece diferentes métodos de hospedagem para a página falsa, incluindo:

- **Ngrok**: Uma ferramenta que permite expor servidores locais para a internet.
- **Serveo**: Um serviço de túnel para acesso pela internet.
- **Localhost**: A página fica acessível apenas dentro da sua rede local.

Escolha o método de hospedagem que deseja utilizar.

### Gerar o Link de Phishing

Dependendo da sua escolha de método de hospedagem, o Zphisher gerará um link que poderá ser enviado para as "vítimas" em um **contexto controlado de testes**. É importante lembrar que esses links só devem ser usados em situações éticas, com permissão e para fins educacionais.

### Ver Credenciais Capturadas

Sempre que alguém tentar fazer login através da página falsa, as credenciais inseridas serão capturadas e exibidas diretamente no terminal do Zphisher. Isso permite verificar quais informações foram coletadas, possibilitando uma análise dos riscos associados ao phishing.

---

## Observações Importantes

### Pentesting Ético

- **Autorização**: Sempre obtenha autorização por escrito antes de executar qualquer tipo de teste de segurança, incluindo ataques simulados de phishing.
- **Ambiente Controlado**: Realize os testes em um ambiente controlado, onde todos os envolvidos estão cientes dos riscos e dos objetivos do teste.
- **Finalidade Educacional**: Use o Zphisher para educar e conscientizar usuários sobre os perigos do phishing, ajudando-os a identificar e evitar ameaças reais.

### Segurança Pessoal

- **Não Utilize para Prejudicar**: Nunca utilize o Zphisher ou qualquer outra ferramenta de phishing para prejudicar outras pessoas. Isso é ilegal e antiético.
- **Responsabilidade Legal**: Ferramentas de phishing, como o Zphisher, são poderosas e podem causar danos significativos se utilizadas de maneira incorreta. O uso indevido pode resultar em **consequências legais graves**.

---

## Exemplos de Uso

### 1. Criando uma Página de Phishing para o Facebook com Ngrok

Neste exemplo, vamos simular a criação de uma página de phishing do **Facebook** utilizando o **Ngrok** para disponibilizar o link na internet.

1. **Iniciar o Zphisher**:
   ```bash
   ./zphisher.sh
   ```

2. **Selecionar o Facebook** no menu de plataformas.

3. **Escolher o Ngrok** como método de hospedagem.

4. O Zphisher gerará um link **Ngrok** que pode ser utilizado em um contexto de teste controlado.

5. Enviar o link para os participantes do teste e observar as credenciais sendo capturadas no terminal.

### 2. Utilizando Serveo para Hospedar uma Página Falsa do Instagram

1. **Iniciar o Zphisher**:
   ```bash
   ./zphisher.sh
   ```

2. **Selecionar o Instagram** no menu.

3. Escolher **Serveo** como método de hospedagem.

4. O link gerado pelo Serveo será exibido e poderá ser compartilhado em um ambiente de teste.

---

## Boas Práticas no Uso do Zphisher

- **Educação e Conscientização**: Utilize o Zphisher como uma ferramenta educativa para treinar funcionários e equipes sobre como identificar e evitar ataques de phishing.
- **Relatórios de Resultados**: Após realizar um teste, compartilhe os resultados com os envolvidos, destacando os pontos que precisam de melhorias e fornecendo dicas para aumentar a segurança.
- **Contexto Realista**: Crie cenários de phishing que sejam realistas, mas evite temas sensíveis que possam causar pânico ou desconforto nos participantes.

---

## Conclusão

O **Zphisher** é uma ferramenta poderosa que pode ser utilizada para fortalecer a segurança cibernética através da simulação de ataques de phishing. Quando usada de forma ética e controlada, ela ajuda a educar e conscientizar os usuários sobre os riscos associados ao phishing, permitindo que as organizações melhorem suas defesas contra ameaças reais.

Lembre-se sempre de utilizar o Zphisher de maneira **responsável** e **legal**, respeitando as leis, políticas internas e a privacidade dos indivíduos envolvidos.

---

