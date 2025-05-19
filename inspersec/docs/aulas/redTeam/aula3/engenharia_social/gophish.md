#GoPhish
---
Nesta sessão, exploraremos em detalhes o que é o GoPhish, como instalá-lo e utilizá-lo para criar campanhas de phishing simuladas, com o objetivo de fortalecer a segurança cibernética e a conscientização dentro de uma organização.

---

## O que é o GoPhish?

O **GoPhish** é uma ferramenta de código aberto projetada para ajudar organizações a realizarem campanhas de phishing simuladas. Ele permite que profissionais de segurança e treinadores de conscientização criem, lancem e monitorem campanhas de phishing de forma eficiente, ajudando a identificar vulnerabilidades humanas e educar os usuários sobre as melhores práticas de segurança.

---

## Por que Utilizar o GoPhish?

- **Conscientização de Segurança**: Ajuda a educar funcionários sobre as ameaças de phishing.
- **Identificação de Vulnerabilidades**: Revela lacunas no conhecimento e práticas de segurança dos usuários.
- **Treinamento Prático**: Fornece experiência realista sem os riscos associados a ataques reais.
- **Medição de Resultados**: Oferece métricas e relatórios detalhados para avaliar a eficácia das campanhas.

---

## Instalando o GoPhish no Ubuntu

Vamos aos passos para instalar o GoPhish em um sistema Ubuntu.

### Pré-requisitos

- Sistema operacional **Ubuntu** (ou outra distribuição Linux compatível).
- Acesso ao terminal com prívilégios de administrador.
- Conexão à internet.

### Passo 1: Baixar o GoPhish

Acesse o site oficial do GoPhish ou o repositório no GitHub para baixar a versão mais recente.

No terminal, você pode usar o comando `wget` para baixar diretamente o pacote. Por exemplo:

```bash
wget https://github.com/gophish/gophish/releases/download/v0.11.0/gophish-v0.11.0-linux-64bit.zip
```

> **Nota**: Verifique a página de lançamentos do GitHub para confirmar a versão mais recente.

### Passo 2: Extrair o Arquivo Baixado

Descompacte o arquivo `.zip` que você baixou:

```bash
unzip gophish-v0.11.0-linux-64bit.zip
```

Isso criará uma pasta com os arquivos do GoPhish.

### Passo 3: Executar o GoPhish

Navegue até o diretório do GoPhish:

```bash
cd gophish
```

Conceda permissão de execução ao arquivo binário (se necessário):

```bash
chmod +x gophish
```

Execute o GoPhish:

```bash
./gophish
```

Ao iniciar, o GoPhish exibiraá informações no terminal, incluindo:

- A porta em que a interface administrativa está sendo executada (por padrão, **3333**).
- A senha temporária gerada para o usuário **admin**.

### Passo 4: Acessar o Painel de Administração

Abra seu navegador e acesse:

```arduino
https://localhost:3333
```

> **Nota**: Como o GoPhish utiliza certificados autoassinados, você receberá um aviso de segurança. Prossiga adicionando uma exceção no navegador.

Faça login com as credenciais:

- **Usuário**: admin
- **Senha**: a senha gerada exibida no terminal.

Após o primeiro login, é recomendável alterar a senha padrão para garantir a segurança do painel administrativo.

---

## Configurando o GoPhish

Com o GoPhish em execução, você pode começar a configurar suas campanhas.

### 1. Configurar o Perfil de Envio (Sending Profile)

Para enviar e-mails, você precisa configurar um servidor **SMTP**.

- Navegue até "Sending Profiles" no menu lateral.
- Clique em "New Profile".
- Preencha os campos:
  - **Name**: Nome descritivo para o perfil.
  - **From**: Endereço de e-mail que aparecerá como remetente.
  - **Host**: Endereço do servidor SMTP (por exemplo, `smtp.seudominio.com`).
  - **Username** e **Password**: Credenciais de autenticação SMTP.
  - **Use TLS**: Marque se o servidor SMTP requer TLS.
- Clique em "Save" para salvar o perfil.

> **Dicas**:
> - Utilize um servidor SMTP que você tenha permissão para usar.
> - Evite utilizar serviços de e-mail que possam bloquear envios suspeitos ou marcar suas mensagens como spam.

### 2. Criar Modelos de E-mail (Email Templates)

- Selecione "Email Templates" no menu.
- Clique em "New Template".
- Preencha os campos:
  - **Name**: Nome do modelo.
  - **Subject**: Assunto do e-mail.
  - **HTML**: Corpo do e-mail em formato HTML.

Você pode utilizar variáveis para personalizar os e-mails: `{{.FirstName}}`, `{{.LastName}}`, `{{.Email}}`, etc.

Adicione links que direcionem para sua página de phishing (landing page).

- Clique em "Save".

**Exemplo de Corpo de E-mail**:

```html
Olá {{.FirstName}},

Notamos uma atividade suspeita em sua conta. Por favor, acesse o link abaixo para verificar suas informações:

<a href="{{.URL}}">Verificar Conta</a>

Atenciosamente,
Equipe de Segurança
```

### 3. Criar Páginas de Phishing (Landing Pages)

- Vá para "Landing Pages".
- Clique em "New Page".
- Você pode criar uma página do zero ou clonar uma existente:
  - Para clonar, insira a URL na opção "Import Site" e clique em "Import".
- Edite o conteúdo conforme necessário.
- Marque "Capture Submitted Data" para registrar dados inseridos pelos usuários.
- Se quiser redirecionar os usuários após a submissão, insira uma URL em "Redirect to URL".
- Clique em "Save".

### 4. Criar Grupos de Usuários (Users & Groups)

- Selecione "Users & Groups".
- Clique em "New Group".
- Insira o **Name** do grupo.
- Adicione usuários manualmente ou importe de um arquivo CSV com as colunas `first_name`, `last_name`, `email`.
- Clique em "Save".

### 5. Criar e Lançar a Campanha

- Acesse "Campaigns".
- Clique em "New Campaign".
- Preencha os detalhes:
  - **Name**: Nome da campanha.
  - **Email Template**: Selecione o modelo criado.
  - **Landing Page**: Escolha a página de phishing.
  - **Sending Profile**: Selecione o perfil de envio.
  - **URL**: O domínio ou IP que será usado nos links dos e-mails.
  - **Launch Date**: Data e hora de início (opcional).
  - **Groups**: Escolha os grupos de usuários.
- Clique em "Launch Campaign".

---

## Monitorando e Analisando Resultados

Após o lançamento, você pode acompanhar o progresso em tempo real:

- **Visão Geral**: Exibe estatísticas gerais como e-mails enviados, abertos, cliques e submissões.
- **Detalhes dos Usuários**: Veja ações individuais de cada usuário.
- **Relatórios**: Gere relatórios para análise e apresentação.

**Métricas Importantes**:

- **Open Rate**: Percentual de usuários que abriram o e-mail.
- **Click Rate**: Percentual que clicou no link.
- **Submission Rate**: Percentual que submeteu dados na página.

Utilize essas informações para identificar áreas que necessitam de treinamento adicional.

---

## Boas Práticas e Considerações Éticas

### Uso Responsável

- **Consentimento**: Tenha aprovação da gerência e, se possível, informe previamente os funcionários sobre possíveis simulações.
- **Privacidade**: Trate os dados coletados com confidencialidade.
- **Objetivo Educacional**: Foque em educar e melhorar a segurança, não em penalizar.

### Conformidade Legal

- Esteja ciente das leis locais, como a **LGPD** no Brasil.
- Evite práticas que possam ser consideradas invasivas ou ilegais.

### Feedback e Treinamento

- Forneça feedback construtivo aos participantes.
- Ofereça treinamentos e recursos para ajudar a reconhecer e-mails de phishing.

---

## Dicas Adicionais

### Segurança do GoPhish

- **Atualizações**: Mantenha o GoPhish atualizado.
- **Acesso Seguro**: Proteja o acesso ao painel administrativo.
- **Firewall**: Configure regras de firewall para limitar o acesso ao servidor.

### Infraestrutura

- Considere utilizar nomes de domínio e certificados SSL válidos para aumentar a credibilidade dos e-mails e páginas.
- Utilize servidores dedicados ou ambientes isolados para evitar conflitos com outros serviços.

### Melhores Práticas em Campanhas

- **Realismo**: Crie cenários realistas, mas evite temas sensíveis ou que possam causar pânico.
- **Diversificação**: Varie os tipos de ataques (e.g., anexos maliciosos, links suspeitos) para cobrir diferentes vetores.
- **Comunicação Pós-Campanha**: Compartilhe os resultados agregados e destaque pontos de melhoria.

---

## Conclusão

O **GoPhish** é uma ferramenta poderosa para fortalecer a segurança cibernética através da conscientização e treinamento. Ao simular ataques de phishing de forma ética e controlada, você pode:

- Educar funcionários sobre as ameaças atuais.
- Reduzir o risco de comprometimento por ataques reais.
- Criar uma cultura de segurança dentro da organização.

Lembre-se sempre de utilizar o GoPhish de maneira responsável, respeitando as leis, políticas internas e a privacidade dos indivíduos.


---

## Anexos

- [Site Oficial do GoPhish](https://getgophish.com/)
- [Repositório no GitHub](https://github.com/gophish/gophish)
- [Documentação Oficial](https://getgophish.com/documentation/)

