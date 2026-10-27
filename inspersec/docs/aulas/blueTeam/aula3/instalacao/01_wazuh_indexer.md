#  Instalação — Wazuh Indexer (Storage)

> **Posição no stack:** Esta é a **primeira** coisa a ser instalada. Todo o resto do SIEM depende que o storage esteja rodando primeiro.

**Referências:**
- [Documentação oficial Wazuh Indexer](https://documentation.wazuh.com/current/getting-started/components/wazuh-indexer.html)
- [Guia de instalação oficial](https://documentation.wazuh.com/current/installation-guide/wazuh-indexer/index.html)
- [Vídeo da aula](https://www.youtube.com/watch?v=O3dtYzkOpxU&list=PLB6hQ_WpB6U0WeroZAfssgRpxW8olnkqy&index=2)

---

## O que é o Wazuh Indexer?

O **Wazuh Indexer** é o componente responsável por **armazenar e indexar** todos os logs de segurança coletados. Ele é um fork do **OpenSearch** (que por sua vez é um fork do **Elasticsearch 7.10.2**, antes da mudança de licença da Elastic).

Quem já conhece OpenSearch ou Elasticsearch vai se sentir em casa — a API e a lógica são praticamente as mesmas.

---

## Conceitos importantes

Antes de instalar, vale entender alguns conceitos fundamentais:

### Tipos de nó

| Tipo | Função |
|---|---|
| **Master Node** | Gerencia configurações do cluster: criar/deletar índices, adicionar/remover nós, alocar shards. **Todo cluster precisa de pelo menos um.** |
| **Data Node** | Armazena os dados e executa operações de busca e agregação. São os "trabalhadores" do cluster — precisam de mais disco que qualquer outro tipo. |
| **Ingest Node** | Pré-processa os dados antes de armazená-los (executa pipelines de transformação). |

### Índices e Shards

- Os dados ficam organizados em **índices** (pense como "tabelas" de um banco de dados).
- Cada índice é dividido em **shards** (fragmentos) que podem ser distribuídos entre os nós.
- Por padrão, os índices do Wazuh são criados **por dia** — isso é importante para não ter que varrer meses de dados ao investigar um incidente pontual.

### Réplicas

- Um **shard réplica** é uma cópia de um shard primário.
- Se um nó cai, os dados que estavam nele ainda existem nas réplicas em outros nós.
- **Custo:** Uma réplica duplica o espaço em disco. Se 1 dia de logs = 100 GB → com 1 réplica = 200 GB.
- **Regra de ouro:** Em clusters multi-nó, sempre configure ao menos 1 réplica.

### Alta Disponibilidade

Em um cluster de 3 nós: se o Master (nó 1) cair, os nós 2 e 3 elegem um novo master automaticamente e o cluster continua funcionando. Quando o nó 1 voltar, ele se reintegra e os dados são rebalanceados.

### Planejamento de disco

Dimensionar o disco não é simples — depende de:
- **Retenção:** Quantos dias/meses de logs você precisa guardar?
- **Tipo de log:** Logs de firewall costumam ser maiores que logs de endpoint.
- Use a [tabela de referência da Wazuh](https://documentation.wazuh.com/current/installation-guide/wazuh-indexer/index.html) como ponto de partida.


### Heap memory (JVM)

O Wazuh Indexer roda na JVM. Duas regras obrigatórias para o heap:
1. **Não use mais que 50% da RAM disponível.**
2. **Não use mais que 32 GB.**

Os valores mínimo (`Xms`) e máximo (`Xmx`) **devem ser iguais** para evitar que a JVM redimensione o heap em runtime (processo extremamente custoso).

---

## Pré-requisitos

- OS suportados: **Debian 11**, Ubuntu 16.x–22.x, CentOS 7–8, Red Hat 7–9, Amazon Linux 2
- Esta demonstração usa **Debian 11 — single node** (1 nó só)
- Acesso root / sudo
- Saber seu **hostname** e seu **IP local** da máquina

Para descobrir:
```bash
hostname          # mostra o hostname
hostname -I       # mostra o IP local
```

---

## 1. Criação dos Certificados

O primeiro passo é gerar os certificados TLS que vão **criptografar a comunicação** entre todos os componentes do Wazuh.

### 1.1 Baixar o script e o arquivo de configuração

```bash
curl -sO https://packages.wazuh.com/4.3/wazuh-certs-tool.sh
curl -sO https://packages.wazuh.com/4.3/config.yml
```

### 1.2 Editar o `config.yml`

Abra o arquivo e substitua os valores de `name` e `ip` com o **hostname** e **IP local** da sua máquina:

```yaml
nodes:
  # Wazuh indexer nodes
  indexer:
    - name: <hostname>
      ip: <ip_local>
    #- name: node-2
    #  ip: <ip_node_2>
    #- name: node-3
    #  ip: <ip_node_3>

  # Wazuh server nodes
  server:
    - name: <hostname>
      ip: <ip_local>
    #  node_type: master
    #- name: wazuh-2
    #  ip: <ip_wazuh-2>
    #  node_type: worker

  # Wazuh dashboard nodes
  dashboard:
    - name: <hostname>
      ip: <ip_local>
```

> 💡 Para um single node, o mesmo hostname e IP aparecem nas três seções (indexer, server e dashboard).

### 1.3 Gerar os certificados

```bash
sudo bash wazuh-certs-tool.sh -A
```

Se aparecer erro de permissão ao executar o script:
```bash
chmod +x wazuh-certs-tool.sh
```

### 1.4 Empacotar os certificados

```bash
tar -cvf ./wazuh-certificates.tar -C ./wazuh-certificates/ .
```

---

## 2. Instalação do Wazuh Indexer

### 2.1 Instalar dependências

```bash
apt-get install debconf adduser procps
apt-get install gnupg apt-transport-https
```

### 2.2 Adicionar a chave GPG do repositório Wazuh

```bash
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | gpg --no-default-keyring --keyring gnupg-ring:/usr/share/keyrings/wazuh.gpg --import && chmod 644 /usr/share/keyrings/wazuh.gpg
```

### 2.3 Adicionar o repositório

```bash
echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | tee -a /etc/apt/sources.list.d/wazuh.list
```

### 2.4 Atualizar e instalar

```bash
apt-get update
apt-get -y install wazuh-indexer
```

---

## 3. Configuração do Wazuh Indexer

Navegue até a pasta de configuração:

```bash
cd /etc/wazuh-indexer/
```

### 3.1 Editar o `opensearch.yml`

```bash
nano opensearch.yml
```

Altere os seguintes campos:

| Campo | O que fazer |
|---|---|
| `network.host` | Coloque o **IP local** da máquina |
| `node.name` | Coloque o **hostname** (mesmo nome usado no `config.yml`) |
| `cluster.initial_master_nodes` | Liste os nomes dos nós master (para single node: só o seu hostname) |
| `discovery.seed_hosts` | Para **single node**: pode deixar comentado. Para multi-nó: liste os IPs dos nós master |
| `plugins.security.nodes_dn` | Distinguished Names dos certificados de cada nó |

Exemplo para single node:
```yaml
network.host: 192.168.1.100
node.name: meu-hostname

cluster.initial_master_nodes:
  - "meu-hostname"

# discovery.seed_hosts comentado para single node

plugins.security.nodes_dn:
  - "CN=meu-hostname,OU=Wazuh,O=Wazuh,L=California,C=US"
```

Para ver o CN exato do certificado gerado:
```bash
openssl x509 -subject -nameopt RFC2253 -noout -in /etc/wazuh-indexer/certs/meu-hostname.pem
```

### 3.2 Comentar a linha `compatibility.override_main_response_version`

Se essa linha existir no arquivo, **comente-a** (adicione `#` na frente):

```yaml
# compatibility.override_main_response_version: true
```

> Isso é necessário para compatibilidade com o Graylog.

---

## 4. Implantação dos Certificados

Substitua `<nome-do-nó-indexer>` pelo nome definido no `config.yml` e execute:

```bash
NODE_NAME=<nome-do-nó-indexer>

mkdir /etc/wazuh-indexer/certs
tar -xf ./wazuh-certificates.tar -C /etc/wazuh-indexer/certs/ ./$NODE_NAME.pem ./$NODE_NAME-key.pem ./admin.pem ./admin-key.pem ./root-ca.pem
mv -n /etc/wazuh-indexer/certs/$NODE_NAME.pem /etc/wazuh-indexer/certs/indexer.pem
mv -n /etc/wazuh-indexer/certs/$NODE_NAME-key.pem /etc/wazuh-indexer/certs/indexer-key.pem
chmod 500 /etc/wazuh-indexer/certs
chmod 400 /etc/wazuh-indexer/certs/*
chown -R wazuh-indexer:wazuh-indexer /etc/wazuh-indexer/certs
```

> 🔒 **Segurança:** Se nenhum outro componente Wazuh vai ser instalado neste servidor, remova o arquivo de certificados:
> ```bash
> rm -f ./wazuh-certificates.tar
> ```

---

## 5. Memory Lock (Evitar uso de SWAP)

O Wazuh Indexer apresenta mau funcionamento quando o sistema usa memória SWAP. É crítico que o processo nunca seja "swappado" para disco.

### 5.1 Habilitar memory lock no `opensearch.yml`

```bash
nano /etc/wazuh-indexer/opensearch.yml
```

Descomente ou adicione:
```yaml
bootstrap.memory_lock: true
```

### 5.2 Remover limite de memória no serviço

```bash
nano /usr/lib/systemd/system/wazuh-indexer.service
```

Na seção `[Service]`, adicione:
```ini
[Service]
LimitMEMLOCK=infinity
```

### 5.3 Configurar o Heap da JVM

```bash
nano /etc/wazuh-indexer/jvm.options
```

Edite as linhas de heap (os valores de Xms e Xmx **devem ser iguais**):

```
# Xms = tamanho inicial do heap
# Xmx = tamanho máximo do heap
-Xms4g
-Xmx4g
```

> ⚠️ Use no máximo 50% da RAM disponível e nunca mais que 32g.

---

## 6. Iniciando o Serviço

```bash
systemctl daemon-reload
systemctl enable wazuh-indexer
systemctl start wazuh-indexer
```

---

## 7. Inicialização do Cluster

Execute o script de inicialização de segurança **uma única vez** (em qualquer nó do cluster):

```bash
/usr/share/wazuh-indexer/bin/indexer-security-init.sh
```

> ℹ️ Isso só precisa ser feito uma vez — não rode em cada nó.

### 7.1 Verificar se o cluster está funcionando

Substitua `<IP_DO_WAZUH_INDEXER>` pelo IP do servidor:

```bash
curl -k -u admin:admin https://<IP_DO_WAZUH_INDEXER>:9200/_cat/nodes?v
```

Se aparecer uma tabela com os nós, o cluster está funcionando! ✅

---

## 8. Instalação do Wazuh Dashboard

O **Wazuh Dashboard** é a interface web que permite interagir com o cluster de forma visual. Ele precisa se comunicar com o Wazuh Indexer já instalado.

### 8.1 Instalar dependências

```bash
apt-get install debhelper tar curl libcap2-bin
```

### 8.2 Adicionar chave GPG e repositório

(Se já foi feito na instalação do indexer, pode pular)

```bash
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | gpg --no-default-keyring --keyring gnupg-ring:/usr/share/keyrings/wazuh.gpg --import && chmod 644 /usr/share/keyrings/wazuh.gpg

echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | tee -a /etc/apt/sources.list.d/wazuh.list

apt-get update
```

### 8.3 Instalar o Wazuh Dashboard

```bash
apt-get -y install wazuh-dashboard
```

### 8.4 Configurar o `opensearch_dashboards.yml`

```bash
nano /etc/wazuh-dashboard/opensearch_dashboards.yml
```

| Campo | Valor |
|---|---|
| `server.host` | `0.0.0.0` (aceita conexões de qualquer IP) |
| `server.port` | `443` |
| `opensearch.hosts` | URL do Wazuh Indexer (ex: `https://localhost:9200`) |
| `opensearch.ssl.verificationMode` | `certificate` |

```yaml
server.host: 0.0.0.0
server.port: 443
opensearch.hosts: https://localhost:9200
opensearch.ssl.verificationMode: certificate
```

### 8.5 Implantar os certificados do Dashboard

```bash
NODE_NAME=<nome-do-dashboard>

mkdir /etc/wazuh-dashboard/certs
tar -xf ./wazuh-certificates.tar -C /etc/wazuh-dashboard/certs/ ./$NODE_NAME.pem ./$NODE_NAME-key.pem ./root-ca.pem
mv -n /etc/wazuh-dashboard/certs/$NODE_NAME.pem /etc/wazuh-dashboard/certs/dashboard.pem
mv -n /etc/wazuh-dashboard/certs/$NODE_NAME-key.pem /etc/wazuh-dashboard/certs/dashboard-key.pem
chmod 500 /etc/wazuh-dashboard/certs
chmod 400 /etc/wazuh-dashboard/certs/*
chown -R wazuh-dashboard:wazuh-dashboard /etc/wazuh-dashboard/certs
```

### 8.6 Iniciar o serviço

```bash
systemctl daemon-reload
systemctl enable wazuh-dashboard
systemctl start wazuh-dashboard
```

### 8.7 Apontar para o Wazuh Manager

```bash
nano /usr/share/wazuh-dashboard/data/wazuh/config/wazuh.yml
```

```yaml
hosts:
  - default:
      url: https://localhost
      port: 55000
      username: wazuh-wui
      password: wazuh-wui
      run_as: false
```

---

## 9. Segurança — Alterar as Senhas Padrão

**Nunca deixe as senhas padrão em produção!**

### 9.1 Trocar todas as senhas do Wazuh Indexer

Em qualquer nó do indexer:

```bash
/usr/share/wazuh-indexer/plugins/opensearch-security/tools/wazuh-passwords-tool.sh --change-all
```

Guarde as senhas geradas — você vai precisar delas.

### 9.2 Atualizar a senha do kibanaserver no Dashboard

Substitua `<senha-do-kibanaserver>` pela senha gerada no passo anterior:

```bash
echo <senha-do-kibanaserver> | /usr/share/wazuh-dashboard/bin/opensearch-dashboards-keystore --allow-root add -f --stdin opensearch.password
```

### 9.3 Reiniciar o Dashboard

```bash
systemctl restart wazuh-dashboard
```

---

## ✅ Resultado Final

Após todos esses passos, você terá:
- O **Wazuh Indexer** rodando e armazenando dados na porta `9200`
- O **Wazuh Dashboard** acessível via browser na porta `443` com HTTPS

Acesse via browser: `https://<IP_DO_SERVIDOR>`

---

## Portas necessárias

| Porta | Protocolo | Componente | Descrição |
|---|---|---|---|
| 9200 | HTTPS | Wazuh Indexer | API REST do cluster |
| 9300 | TCP | Wazuh Indexer | Comunicação interna entre nós |
| 443 | HTTPS | Wazuh Dashboard | Interface web |

---

*Próximo passo: [Instalação do Graylog (Log Ingestion)](02_graylog.md) 🔜*
