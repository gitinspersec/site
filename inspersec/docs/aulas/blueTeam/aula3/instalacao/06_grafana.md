# Instalacao e Configuracao — Grafana (Visualizacao e Dashboards)

> **Posicao no stack:** Sexta etapa. Apos coletar, normalizar e rotear nossos logs para o Wazuh Indexer atraves do Graylog, agora conectamos o **Grafana** ao backend para criar paineis visuais, mapas geograficos de ameacas, correlacoes de rede e graficos de series temporais.

**Referencias:**
- [Documentacao oficial do Grafana](https://grafana.com/docs/grafana/latest/)
- [Configuracao de Elasticsearch no Grafana](https://grafana.com/docs/grafana/latest/datasources/elasticsearch/configure/)
- [NetSage Sankey Panel Plugin](https://grafana.com/grafana/plugins/netsage-sankey-panel/)
- [Painel Geomap no Grafana](https://grafana.com/docs/grafana/latest/panels-visualizations/visualizations/geomap/)
- [Post da aula — SOCFortress](https://socfortress.medium.com/part-6-best-open-source-siem-dashboards-5dad09fa4d0e)

---

## Por que usar o Grafana?

Alem do Wazuh Dashboard nativo (OpenSearch Dashboards), o **Grafana** oferece recursos fundamentais para um SOC moderno:

- **Performance superior:** Renderizacao extremamente rapida e fluida para consultas densas.
- **Painéis avancados:** Suporte a plugins nativos e de comunidade como diagramas Sankey, graficos de barras horizontais, mapas de calor e visualizacoes geograficas interativas.
- **Data Links dinamicos:** Capacidade de criar links diretos nos eventos para fontes externas de inteligencia (como o banco de vulnerabilidades NIST NVD) ou abrir a investigacao no Grafana Explore com um unico clique.
- **Multi-fonte:** Capacidade de cruzar logs do Wazuh Indexer com metricas do InfluxDB, Prometheus ou bancos relacionais no mesmo painel.

---

## Instalacao do Grafana (caso nao esteja instalado)

Se o Grafana Server ainda nao estiver presente no servidor, execute os comandos abaixo como `root`:

```bash
apt-get install -y apt-transport-https software-properties-common wget gnupg
wget -q -O /usr/share/keyrings/grafana.key https://apt.grafana.com/gpg.key
echo "deb [signed-by=/usr/share/keyrings/grafana.key] https://apt.grafana.com stable main" | tee -a /etc/apt/sources.list.d/grafana.list
apt-get update
apt-get install -y grafana
systemctl daemon-reload
systemctl enable grafana-server
systemctl start grafana-server
```

> O Grafana roda por padrao na porta `3000` (HTTP). Acesse via navegador em `http://<IP_DO_SERVIDOR>:3000`.  
> Credenciais padrao: usuario `admin`, senha `admin`.

---

## Passo 1 — Adicionar a Data Source do Wazuh Indexer

Como o Wazuh Indexer e compativel com a API do Elasticsearch/OpenSearch, utilizamos a fonte de dados do tipo **Elasticsearch** no Grafana.

### No Grafana WebUI

1. Acesse o menu lateral e va em **Administration > Data sources** (ou **Connections > Data sources**).
2. Clique no botao **Add data source**.
3. Selecione o tipo **Elasticsearch**.
4. Configure as secoes principais:

#### Secao HTTP
- **URL:** `https://<IP_DO_WAZUH_INDEXER>:9200`
- **Access:** `Server (default)`

#### Secao Auth
- **Basic auth:** Habilitado
- **Skip TLS Verify:** Habilitado (para certificados autoassinados da instalacao local)

#### Secao Basic Auth Details
- **User:** `admin` (ou o usuario interno criado no Wazuh Indexer)
- **Password:** A senha definida durante a instalacao do Wazuh Indexer

#### Secao Elasticsearch details
- **Index name:** `wazuh_alerts*`
- **Time field name:** `timestamp`
- **Version:** `7.10+` (ou a versao suportada correspondente ao OpenSearch 1.x)

---

### Configuracao de Data Links na Data Source

Os **Data Links** criam atalhos dinamicos nos detalhes dos logs para agilizar o fluxo de analise do SOC:
- O **Link 1** extrai o codigo CVE de vulnerabilidades e redireciona diretamente para a base oficial da NIST (NVD).
- O **Link 2** utiliza o identificador unico do log (`_id`) para abrir uma aba de investigacao profunda no Grafana Explore.

Na pagina de configuracao da fonte de dados, desca ate a secao **Data links** e adicione as duas regras abaixo:

#### Link 1 — Consultar CVE na base do NVD

| Parametro | Valor |
|---|---|
| **Field** | `^data_vulnerability\.cve$` |
| **URL** | `https://nvd.nist.gov/vuln/detail/${__value.raw}` |
| **URL Label** | `NIST CVE DATABASE` |
| **Internal link** | Desativado |

#### Link 2 — Abrir detalhes do evento no Explore

| Parametro | Valor |
|---|---|
| **Field** | `^_id$` |
| **URL** | `https://<IP_DO_GRAFANA>:3000/explore?left=%5B%22now-6h%22,%22now%22,%22WAZUH%22,%7B%22refId%22:%22A%22,%22query%22:%22_id:${__value.raw}%22,%22alias%22:%22%22,%22metrics%22:%5B%7B%22id%22:%221%22,%22type%22:%22logs%22,%22settings%22:%7B%22limit%22:%22500%22%7D%7D%5D,%22bucketAggs%22:%5B%5D,%22timeField%22:%22timestamp%22%7D%5D` |
| **URL Label** | `VIEW EVENT DETAILS` |
| **Internal link** | Desativado |

> **Atencao ao Link 2:** Substitua `<IP_DO_GRAFANA>:3000` pelo IP real e porta acessiveis pelo seu navegador. A consulta assume que o nome da data source no Grafana e exatamente `WAZUH`. Se voce utilizou outro nome (ex: `Elasticsearch`), substitua `"WAZUH"` na URL pelo nome atribuido.

Clique em **Save & test**. Se a configuracao estiver correta, aparecera a confirmacao em verde: *"Index OK. Returned 1 index..."*.

---

## Passo 2 — Separar os Rule Groups no Graylog (Extractors)

O Wazuh gera regras categorizadas em listas de grupos (exemplo: `rule.groups` contendo `["packetbeat", "dns", "sysmon"]`). Quando indexado como string separada por virgula ou array bruto, pode dificultar consultas diretas como `rule_group2:packetbeat`.

Para resolver isso, criamos extratores no Graylog para decompor o campo em tres atributos indexados individuais: `rule_group1`, `rule_group2` e `rule_group3`.

### No Graylog WebUI

1. Va em **System > Inputs**.
2. Localize o input do Wazuh (ex: `WAZUH EVENTS FLUENT BIT`) e clique em **Manage extractors**.
3. Em uma mensagem de exemplo que contenha o campo `rule_groups` (ou `rule_group`), clique na seta ao lado do campo e selecione **Create extractor**.
4. Selecione o tipo de extrator: **Split & index**.
5. Configure o delimitador como virgula (`,`).
6. Crie os tres extratores sequenciais:

| Extrator | Split by | Target index | Store as field | Extractor title |
|---|---|---|---|---|
| **1** | `,` | `0` | `rule_group1` | `Split rule_group1` |
| **2** | `,` | `1` | `rule_group2` | `Split rule_group2` |
| **3** | `,` | `2` | `rule_group3` | `Split rule_group3` |

7. Use o botao **Try** para validar a quebra dos campos e clique em **Save** em cada um.

Agora, eventos como alertas do Packetbeat terao seus grupos acessiveis diretamente nos campos `rule_group1` e `rule_group2`.

---

## Passo 3 — Painel Sankey (Fluxo de Rede Origem e Destino)

O painel Sankey permite visualizar fluxos relacionais em grafos direcionados, mostrando claramente quais IPs de origem (`data_source_ip`) estao enviando dados para quais IPs de destino (`data_destination_ip`).

### 1. Instalacao do Plugin NetSage Sankey Panel

Execute os comandos no terminal como `root`:

```bash
# 1. Ajuste permissoes para o Grafana ler os certificados caso utilize caminhos customizados
chgrp grafana /etc/ssl/private /etc/ssl/private/indexer-key.pem 2>/dev/null || true
chmod 750 /etc/ssl/private 2>/dev/null || true
chmod 640 /etc/ssl/private/indexer-key.pem 2>/dev/null || true

# 2. Instale o plugin NetSage Sankey Panel via CLI do Grafana
grafana-cli plugins install netsage-sankey-panel

# 3. Garanta permissoes adequadas na pasta de plugins
chown -R grafana:grafana /var/lib/grafana/plugins

# 4. Reinicie o servico do Grafana
systemctl restart grafana-server
```

---

### 2. Criacao da Visualizacao Sankey no Dashboard

1. No Grafana, crie um novo dashboard (**Dashboards > New dashboard > Add visualization**).
2. Selecione a fonte de dados do **Elasticsearch**.
3. No canto superior direito, altere o tipo de visualizacao para **Sankey Panel** (NetSage Sankey Panel).
4. Na aba **Queries**, configure a consulta:
   - **Query type:** `Metrics`
   - **Lucene Query:**
     ```lucene
     rule_groups: packetbeat
     ```
     *(ou `rule_group2: packetbeat` ou `rule_groups: dns`)*
   - **Metric:** `Count`
   - **Group By:**
     - Remova o agrupamento padrao `Date Histogram`.
     - Clique em **Add grouping** e escolha: `Terms` -> Campo: `data_source_ip.keyword` (ou `data_source_ip`), Size: `10`.
   - **Then By:**
     - Clique no `+` e selecione: `Terms` -> Campo: `data_destination_ip.keyword` (ou `data_destination_ip`), Size: `10`.
5. No menu lateral direito de configuracao do painel:
   - **Title:** `Fluxo de Conexoes de Rede (Sankey)`
   - **Value Field:** `Count`
   - **Layout Iterations:** `15` (ou maior, para suavizar os arcos do diagrama)
6. Ajuste o intervalo de tempo para as ultimas 24 horas (`Last 24 hours`) e salve o painel.

---

## Passo 4 — Habilitar Geolocalizacao (GeoIP) no Graylog

Para mapear conexoes e consultas DNS em um mapa-mundi, precisamos resolver os enderecos IP externos em paises e cidades. Faremos isso carregando as bases MaxMind GeoLite2 no Graylog.

### 1. Download das bases MaxMind no servidor do Graylog

Execute no terminal:

```bash
cd /etc/graylog/server
wget https://github.com/socfortress/Wazuh-Rules/releases/download/1.0/GeoLite2-City.mmdb
wget https://github.com/socfortress/Wazuh-Rules/releases/download/1.0/GeoLite2-ASN.mmdb
chown -R graylog:graylog /etc/graylog/server/*.mmdb
systemctl restart graylog-server
```

### 2. Habilitar o Geo-Location Processor no Graylog WebUI

1. Acesse o Graylog e navegue ate **System > Configurations**.
2. Localize o quadro **Geo-Location Processor** e clique em **Update**.
3. Marque a opcao **Enable geo-location processor**.
4. Preencha os caminhos dos arquivos:
   - **Path to MaxMind City database:** `/etc/graylog/server/GeoLite2-City.mmdb`
   - **Path to MaxMind ASN database:** `/etc/graylog/server/GeoLite2-ASN.mmdb`
5. Clique em **Save**.

### 3. Ajustar a Ordem dos Message Processors

1. Na mesma pagina (**System > Configurations**), localize a secao **Message Processors Configuration**.
2. Clique em **Update**.
3. Arraste o processador **GeoIP Resolver** para a **ultima posicao da fila**.
   - Isso garante que todos os campos ja foram parseados e extraidos antes de o Graylog tentar resolver a localizacao geografica do IP.
4. Clique em **Save**.

---

## Passo 5 — Visualizacao Geografica de Conexoes DNS (Geomap)

O painel nativo **Geomap** do Grafana plota no mapa-mundi a distribuicao dos paises de destino das consultas DNS capturadas pelo Packetbeat e enriquecidas com o codigo do pais (`data_dns_resolved_ip_country_code`).

> **Nota:** Como os IPs da rede interna (`192.168.x.x` ou `10.x.x.x`) sao faixas privadas RFC 1918, a geolocalizacao baseia-se no IP publico externo resolvido pela consulta DNS e nao no IP local do computador.

### Configuracao da Query no Grafana

1. No dashboard, clique em **Add visualization** e aponte para a fonte de dados do **Elasticsearch**.
2. No canto superior direito, selecione o painel **Geomap**.
3. Na aba **Queries**, configure:
   - **Query type:** `Metrics`
   - **Lucene Query:**
     ```lucene
     rule_groups: packetbeat AND _exists_:data_dns_resolved_ip_country_code
     ```
   - **Metric:** `Count`
   - **Group By:**
     - Remova o `Date Histogram`.
     - Adicione `Terms` -> Campo: `data_dns_resolved_ip_country_code.keyword` (ou `data_dns_resolved_ip_country_code`), Size: `50`.

### Configuracao das Propriedades do Painel Geomap

No menu lateral direito:
1. **Title:** `Destino Geografico de Consultas DNS (Geomap)`
2. **Map view:**
   - **Initial view:** `World` (ou Coordenadas: `0, 0`, Zoom: `1`)
3. **Data layer:**
   - **Layer type:** `Markers` (ou `Lookup`)
   - **Location mode:** `Lookup`
   - **Lookup field:** `data_dns_resolved_ip_country_code`
   - **Country display:** ISO 3166-1 alpha-2
   - **Size:** Mapeado para o valor de `Count`
   - **Color:** Paleta de calor baseada no volume de requisicoes (ex: *Green-Yellow-Red*)
4. Salve o painel.

---

## Passo 6 — Monitoramento Temporal de Trafego de Rede por Agente (Time Series)

O grafico de series temporais (**Time Series**) permite acompanhar o volume historico de eventos de rede capturados pelo Packetbeat, distribuindo as linhas por agente/host de origem (`agent_name`).

### Configuracao da Query no Grafana

1. No dashboard, adicione uma nova visualizacao com a fonte **Elasticsearch**.
2. No canto superior direito, selecione o painel **Time series**.
3. Na aba **Queries**, configure os parametros:
   - **Query type:** `Metrics`
   - **Lucene Query:**
     ```lucene
     rule_group2:packetbeat
     ```
   - **Metric:** `Count`
   - **Group By:**
     - **Date Histogram:** Campo `timestamp`, Interval: `auto` (ou `$__interval`)
   - **Then By:**
     - Clique no botao `+` e adicione um segundo nivel de agrupamento:
     - **Terms:** Campo `agent_name.keyword` (ou `agent_name`), Order: `Top`, Size: `10`

### Configuracao das Propriedades do Painel Time Series

No menu lateral direito:
1. **Title:** `Volume de Eventos Packetbeat por Agente (Time Series)`
2. **Graph styles:**
   - **Style:** `Lines`
   - **Line width:** `2`
   - **Fill opacity:** `10%`
   - **Gradient mode:** `None` ou `Opacity`
3. **Legend:**
   - **Visibility:** Habilitado
   - **Display mode:** `Table`
   - **Placement:** `Bottom`
   - **Legend values:** `Total`, `Max`, `Last`
4. Salve o painel.

---

## Passo 7 — Grafico de Processos Mais Ativos (Bar Chart / Ranking)

Este painel exibe um ranking horizontal em barras com os executaveis e binarios do sistema que mais geraram requisicoes de rede, utilizando o campo `data_process_executable` coletado pelo Packetbeat e Wazuh.

Isso permite identificar rapidamente processos anomalos gerando conexoes de rede suspeitas (como `powershell.exe`, `curl`, `nc` ou binarios em `/tmp/`).

### Configuracao da Query no Grafana

1. No dashboard, adicione uma nova visualizacao com a fonte **Elasticsearch**.
2. No canto superior direito, altere o tipo de visualizacao para **Bar chart**.
3. Na aba **Queries**, configure os campos:
   - **Query type:** `Metrics`
   - **Lucene Query:**
     ```lucene
     rule_group2:packetbeat AND _exists_:data_process_executable
     ```
   - **Metric:** `Count`
   - **Group By:**
     - Remova o agrupamento padrao `Date Histogram`.
     - Adicione `Terms` -> Campo: `data_process_executable.keyword` (ou `data_process_executable`), Order: `Top`, Size: `10`

### Configuracao das Propriedades do Painel Bar Chart

No menu lateral direito:
1. **Title:** `Top Processos com Conexoes de Rede (Ranking)`
2. **Bar chart options:**
   - **Orientation:** `Horizontal` (facilita a leitura dos caminhos de executaveis)
   - **Show values:** `Always`
   - **Bar width:** `0.7`
   - **Color mode:** `By value` ou `Palette classic`
3. **Tooltip:** `Single`
4. Salve o painel.

---

## Resumo das Visualizacoes do Dashboard SIEM

| Painel | Tipo no Grafana | Campo Agrupado | Finalidade Operacional |
|---|---|---|---|
| **Fluxo de Rede** | Sankey Panel | `data_source_ip` -> `data_destination_ip` | Mapear conexoes entre ativos internos e destinos externos |
| **Geomap DNS** | Geomap | `data_dns_resolved_ip_country_code` | Identificar acessos a servidores e dominios em paises de risco |
| **Trafego por Agente** | Time Series | `timestamp` + `agent_name` | Detectar anomalias temporais e picos de trafego por máquina |
| **Processos Ativos** | Bar Chart | `data_process_executable` | Ranking de executaveis e binarios responsaveis pelo trafego |

---

*<- Etapa anterior: [Graylog — Log Routing](05_graylog_routing.md)*
*Proximo passo: Enriquecimento e Threat Intelligence (OpenCTI / MISP)*