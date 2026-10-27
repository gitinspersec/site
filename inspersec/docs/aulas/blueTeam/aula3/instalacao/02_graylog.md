#  Instalação — Graylog (Log Ingestion)

> **Posição no stack:** Segunda etapa. O Graylog recebe os logs das fontes, os normaliza e os envia para o Wazuh Indexer já instalado.

**Referências:**
- [Documentação oficial Graylog](https://docs.graylog.org/)
- [Vídeo da aula](https://www.youtube.com/watch?v=63pNANfZHIk&list=PLB6hQ_WpB6U0WeroZAfssgRpxW8olnkqy&index=3)

---

## Por que Graylog?

Com várias ferramentas de ingestão de logs disponíveis no mercado (Filebeat, Logstash, NIFI, Fluent-bit...), por que escolher o Graylog?

###  Compatibilidade com o Wazuh Indexer

Precisamos de uma ferramenta que consiga **ler e escrever** no nosso backend Wazuh Indexer (que é um fork do OpenSearch 1.3). O Graylog é uma das poucas ferramentas que suporta exatamente essa versão.

>  **CRÍTICO:** O Graylog suporta até **OpenSearch 1.3** e **Elasticsearch 7.10.x ou inferior**.  
> Versões mais novas do Elasticsearch ou OpenSearch 2.x **vão quebrar o Graylog**. Não ignore isso!

---

###  Múltiplos Inputs Suportados

Talvez você queira ingerir logs da AWS, de firewalls e de endpoints ao mesmo tempo. O Graylog permite configurar **inputs** — portas que ele fica "ouvindo" para receber logs de diferentes fontes.

Tipos de input suportados (entre muitos outros):

| Input | Descrição |
|---|---|
| **AWS CloudTrail** | Logs de auditoria da AWS |
| **Beats** | Compatível com Filebeat, Winlogbeat, etc. |
| **GELF** | Formato nativo do Graylog |
| **Syslog** | Padrão universal de logs de rede/SO |
| **Raw/Plaintext** | Texto puro, para fontes simples |

---

###  Gerenciamento de Índices

Índices são a forma como o OpenSearch/Elasticsearch organizam os dados. Sem gerenciamento adequado, índices antigos acumulam dados no disco até o ponto em que não há mais espaço para gravar logs novos.

O Graylog permite configurar políticas de **retenção automática**: índices antigos que não precisamos mais são deletados automaticamente, liberando espaço para os dados novos.

---

###  Normalização de Logs

Fontes diferentes nomeiam os campos de forma diferente. Por exemplo:

| Fonte | Campo que armazena o IP de origem |
|---|---|
| Firewall | `source_ip_ipv4` |
| Sysmon (Windows) | `data_win_eventdata_sourceIp` |

Ambos contêm a mesma informação! Com o Graylog, mapeamos esses campos para um nome universal — ex: `src_ip`.

**Por que isso importa?**
- Uma única query de busca cobre todas as fontes
- Dashboards e alertas criados uma vez funcionam para todos os tipos de log
- Quando uma nova fonte é adicionada, basta mapear os campos — sem recriar tudo

---

###  Enriquecimento via API

O Graylog pode consultar feeds de inteligência de ameaças (MISP, VirusTotal, etc.) **em tempo real**, antes mesmo de armazenar o log no Wazuh Indexer.

Isso significa que quando um IP malicioso conhecido aparecer nos seus logs, ele já chega ao storage marcado com o contexto de ameaça — sem precisar de processamento posterior.

---

###  Sem Perda de Dados

Se o Wazuh Indexer ficar indisponível por algum motivo, o Graylog detecta o problema e **grava os logs em disco** temporariamente. Quando o backend voltar ao normal, os logs são enviados sem perda.

---

## Instalação

Esta demonstração usa **Debian 11**. Os passos são similares para outras distros baseadas em Debian/Ubuntu.

---

### Etapa 1 — Pré-requisitos

Atualize o sistema e instale as dependências necessárias:

```bash
sudo apt update && sudo apt upgrade
sudo apt install apt-transport-https openjdk-11-jre-headless uuid-runtime pwgen dirmngr gnupg wget
```

---

### Etapa 2 — MongoDB

O Graylog usa o **MongoDB** para armazenar **metadados de configuração** (usuários, streams, dashboards) — não os logs em si. Os logs vão para o Wazuh Indexer.

O MongoDB pode ser instalado no mesmo servidor do Graylog ou em um servidor dedicado.

> ⚠️ **Atenção — distro específica:** Os comandos abaixo são para **Debian 11 (Bullseye)**. Se você estiver usando uma distribuição diferente (Ubuntu, CentOS, Red Hat, etc.), o comando de adicionar o repositório muda. Consulte a [documentação oficial do MongoDB](https://www.mongodb.com/docs/manual/installation/) e selecione sua distro para obter o comando correto.

```bash
# Importar a chave GPG do MongoDB
curl -fsSL https://www.mongodb.org/static/pgp/server-6.0.asc | \
  gpg --dearmor -o /usr/share/keyrings/mongodb-server-6.0.gpg

# Adicionar o repositório  ← este comando é específico para Debian 11 (Bullseye)
# Se sua distro for diferente, substitua esta linha pelo comando da documentação oficial
echo "deb [ signed-by=/usr/share/keyrings/mongodb-server-6.0.gpg ] https://repo.mongodb.org/apt/debian bullseye/mongodb-org/6.0 main" | \
  tee /etc/apt/sources.list.d/mongodb-org-6.0.list

# Instalar
apt-get update
apt-get install -y mongodb-org

# Habilitar e iniciar
systemctl daemon-reload
systemctl enable mongod
systemctl start mongod
```

---

### Etapa 3 — Graylog

Adicione o repositório do Graylog e instale:

> ⚠️ **Atenção — distro específica:** O pacote `.deb` abaixo é para **Debian/Ubuntu**. Se você estiver usando CentOS, Red Hat ou outra distro, consulte a [documentação oficial do Graylog](https://go2docs.graylog.org/current/downloading_and_installing_graylog/installing_graylog.htm) para obter o pacote e os comandos corretos para sua distribuição.

```bash
# Baixar e adicionar o repositório  ← pacote .deb, específico para Debian/Ubuntu
wget https://packages.graylog2.org/repo/packages/graylog-5.0-repository_latest.deb
dpkg -i graylog-5.0-repository_latest.deb

# Atualizar e instalar
apt-get update
apt-get install graylog-server
```

---

### Etapa 4 — Certificados (HTTPS com Wazuh Indexer)

Como o Wazuh Indexer usa HTTPS com os certificados que geramos na etapa anterior, precisamos informar ao Java do Graylog que ele deve confiar nesses certificados.

#### 4.1 — Copiar o certificado raiz para o Graylog

```bash
mkdir -p /etc/graylog/server/certs

# Copie o root-ca.pem gerado na instalação do Wazuh Indexer
cp /etc/wazuh-indexer/certs/root-ca.pem /etc/graylog/server/certs/root-ca.pem
```

#### 4.2 — Importar o certificado no Java KeyStore

```bash
# Localizar o cacerts padrão do Java
JAVA_CACERTS=$(find /usr/lib/jvm -name "cacerts" | head -1)

# Copiar o cacerts para a pasta do Graylog
cp $JAVA_CACERTS /etc/graylog/server/certs/cacerts

# Importar o certificado root da Wazuh
keytool -importcert \
  -keystore /etc/graylog/server/certs/cacerts \
  -storepass changeit \
  -alias wazuh-root-ca \
  -file /etc/graylog/server/certs/root-ca.pem \
  -noprompt
```

#### 4.3 — Configurar as opções Java do Graylog

```bash
nano /etc/default/graylog-server
```

Adicione a seguinte linha:

```bash
GRAYLOG_SERVER_JAVA_OPTS="$GRAYLOG_SERVER_JAVA_OPTS -Dlog4j2.formatMsgNoLookups=true -Djavax.net.ssl.trustStore=/etc/graylog/server/certs/cacerts -Djavax.net.ssl.trustStorePassword=changeit"
```

> 💡 Você também pode ajustar o **heap de memória Java** neste mesmo arquivo, ex:
> ```bash
> GRAYLOG_SERVER_JAVA_OPTS="-Xms2g -Xmx2g ..."
> ```

---

### Etapa 5 — Editar o arquivo de configuração

```bash
nano /etc/graylog/server/server.conf
```

#### 5.1 — Gerar e configurar o `password_secret`

O `password_secret` é usado para criptografar senhas e sessões. Gere um com:

```bash
pwgen -N 1 -s 96
```

Cole o resultado no arquivo de configuração:

```
password_secret = <resultado_do_comando_acima>
```

#### 5.2 — Gerar e configurar o `root_password_sha2`

Esta é a senha do usuário administrador (`admin`) da interface web. Para gerar o hash SHA-256:

```bash
echo -n "Enter Password: " && head -1 </dev/stdin | tr -d '\n' | sha256sum | cut -d" " -f1
```

> O comando vai pedir que você digite sua senha. O hash gerado é o que vai no arquivo de configuração — a senha original nunca é armazenada.

Cole no arquivo:

```
root_password_sha2 = <hash_sha256_da_sua_senha>
```

#### 5.3 — Configurar a conexão com o Wazuh Indexer

Primeiro, crie um **internal user** no Wazuh Indexer para o Graylog usar. Você pode fazer isso pela interface web do Wazuh Dashboard em: **Security → Internal Users → Create user**.

>  **Obs importante:** O Wazuh Indexer vai retornar erro se a senha do usuário contiver **caracteres especiais**. Use apenas letras e números na senha desse usuário.

Depois, edite o campo no `server.conf`:

```
elasticsearch_hosts = https://<usuario>:<senha>@<ip_do_wazuh_indexer>:9200
```

Exemplo:

```
elasticsearch_hosts = https://graylog:SuaSenhaAqui@192.168.1.100:9200
```

#### 5.4 — Configurar o endereço da interface web

```
http_bind_address = 0.0.0.0:9000
```

---

### Etapa 6 — Iniciar o Graylog

```bash
systemctl daemon-reload
systemctl enable graylog-server
systemctl start graylog-server
```

Verifique se o serviço está rodando:

```bash
systemctl --type=service --state=active | grep graylog
```

Se aparecer `graylog-server.service loaded active running`, está funcionando. ✅

---

##  Resultado Final

Após todos esses passos, você terá:
- O **Graylog** rodando e acessível via browser na porta `9000`
- Graylog conectado ao **Wazuh Indexer** via HTTPS com certificados confiáveis
- Pronto para receber logs via inputs configuráveis

Acesse via browser: `http://<IP_DO_SERVIDOR>:9000`

Login padrão: usuário `admin` + a senha que você usou para gerar o hash SHA-256.

---

## Portas necessárias

| Porta | Protocolo | Descrição |
|---|---|---|
| 9000 | HTTP | Interface web do Graylog |
| 12201 | UDP/TCP | Input GELF |
| 514 | UDP/TCP | Input Syslog |
| 5044 | TCP | Input Beats (Filebeat, Winlogbeat...) |

> As portas de input variam conforme os inputs que você configurar na interface web.

---

## Próximos passos na interface web

Após o primeiro login, configure:

1. **Inputs** — Abra as portas para receber logs das suas fontes
2. **Index Sets** — Defina a política de retenção (ex: manter os últimos 30 dias)
3. **Streams** — Roteie os logs para os index sets corretos
4. **Pipelines** — Configure as regras de normalização de campos (ex: mapear `source_ip_ipv4` → `src_ip`)

---

*← Etapa anterior: [Wazuh Indexer (Storage)](01_wazuh_indexer.md)*  
*Próximo passo: [Wazuh Manager (Log Analysis)](03_wazuh_manager.md) 🔜*
