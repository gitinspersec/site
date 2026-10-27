# Instalacao — Wazuh Agent (Endpoint Monitoring)

> **Posicao no stack:** Quarta etapa. O Wazuh Agent e instalado nos endpoints (servidores, desktops, VMs) e envia os logs coletados para o Wazuh Manager, que ja instalamos na etapa anterior.

**Referencias:**
- [Documentacao oficial — Deploy Wazuh Agents](https://documentation.wazuh.com/current/installation-guide/wazuh-agent/index.html)
- [Sysmon — Microsoft Sysinternals](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon)
- [Sysmon Modular Config — Olaf Hartong](https://github.com/olafhartong/sysmon-modular)
- [Video da aula](https://www.youtube.com/watch?v=yBVyhBv9I0A)
- [Post da aula — SOCFortress](https://socfortress.medium.com/part-4-wazuh-agent-install-endpoint-monitoring-f24f6a0464ac)

---

## O que e o Wazuh Agent?

O **Wazuh Agent** e um processo leve instalado em cada endpoint que voce quer monitorar. Ele e responsavel por:

- Coletar logs locais (Event Viewer, Syslog, arquivos de log de aplicacoes)
- Monitorar integridade de arquivos (FIM)
- Executar varreduras de vulnerabilidades e benchmarks de hardening
- Enviar tudo isso para o **Wazuh Manager** via porta `1514`

Sem o agente, o Manager nao tem dados para analisar. O agente e o sensor dos seus endpoints.

---

## Por que o agente padrao nao e suficiente?

Por padrao, o Wazuh Agent no Windows coleta eventos do **Event Viewer** (Application, Security, System). Isso ja e util, mas **nao e suficiente para uma operacao de seguranca moderna**.

O Event Viewer nativo nao captura, por exemplo:
- Criacao de processos com argumentos completos de linha de comando
- Conexoes de rede feitas por processos especificos
- Carregamento de drivers e DLLs
- Actividades de PowerShell em detalhes

Para preencher essas lacunas, precisamos de ferramentas adicionais:

| Ferramenta | O que adiciona |
|---|---|
| **Sysmon** | Visibilidade profunda de processos, conexoes de rede, manipulacao de arquivos, comandos executados |
| **Packetbeat** | Captura de trafico de rede em nvel de protocolo (DNS, HTTP, TLS, etc.) |

---

## Fluxo completo com o agente

```
Endpoint
  |-- Wazuh Agent         (coleta logs do sistema)
  |-- Sysmon              (logs de processos, rede, arquivos)
  |-- Packetbeat          (trafico de rede em JSON)
        |
        | (porta 1514 TCP)
        v
   Wazuh Manager
        |
        v
   alerts.json --> Fluent-Bit --> Graylog --> Wazuh Indexer
```

---

## Metodo 1 — Deploy via Wazuh Dashboard (recomendado)

O Wazuh Dashboard gera automaticamente o comando de instalacao correto para cada OS. Este e o metodo mais simples e menos suscetivel a erros.

### Passos

1. Acesse o **Wazuh Dashboard** em `https://<IP_DO_SERVIDOR>`
2. Navegue ate **Agents management > Summary**
3. Clique em **Deploy new agent**
4. Selecione o sistema operacional do endpoint
5. Preencha:
   - **Server address:** IP ou hostname do Wazuh Manager
   - **Agent name:** Nome do endpoint (opcional, mas recomendado)
   - **Agent group:** Selecione o grupo criado na aula anterior (`linux` ou `windows`)
   - **Enrollment password:** A senha configurada em `/var/ossec/etc/authd.pass`
6. O dashboard vai gerar um comando pronto — copie e execute no endpoint com privilegios de administrador/root

---

## Metodo 2 — Install Manual no Windows

### Etapa 1 — Baixar o instalador

Acesse [packages.wazuh.com](https://packages.wazuh.com/4.x/windows/) e baixe o arquivo `.msi` da versao mais recente.

### Etapa 2 — Instalar via PowerShell (como administrador)

Substitua os valores entre `< >` pelos seus dados reais:

```powershell
msiexec.exe /i wazuh-agent-4.x.x-1.msi /q `
  WAZUH_MANAGER="<IP_DO_MANAGER>" `
  WAZUH_REGISTRATION_PASSWORD="<SENHA_DO_AUTHD>" `
  WAZUH_AGENT_GROUP="windows"
```

> Execute o PowerShell como Administrador. O parametro `/q` faz a instalacao silenciosa (sem interface grafica).

### Etapa 3 — Iniciar o servico

```powershell
Start-Service wazuhsvc
```

### Etapa 4 — Verificar o registro

Verifique se o agente se registrou com sucesso no Manager:

```powershell
# Verificar status do servico
Get-Service wazuhsvc

# Verificar estado de conexao
Select-String -Path "C:\Program Files (x86)\ossec-agent\wazuh-agent.state" -Pattern "^status"
```

O status deve retornar **"connected"** quando o agente estiver comunicando com o Manager.

Para verificar no lado do Manager:

```bash
# No servidor do Wazuh Manager
tail -f /var/ossec/logs/ossec.log
```

---

## Metodo 3 — Install Manual no Linux

### Ubuntu / Debian

```bash
# Adicionar chave GPG e repositorio
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | gpg --no-default-keyring --keyring gnupg-ring:/usr/share/keyrings/wazuh.gpg --import && chmod 644 /usr/share/keyrings/wazuh.gpg

echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | tee -a /etc/apt/sources.list.d/wazuh.list

apt-get update

# Instalar o agente apontando para o Manager
WAZUH_MANAGER="<IP_DO_MANAGER>" \
WAZUH_REGISTRATION_PASSWORD="<SENHA_DO_AUTHD>" \
WAZUH_AGENT_GROUP="linux" \
apt-get install wazuh-agent

# Habilitar e iniciar
systemctl daemon-reload
systemctl enable wazuh-agent
systemctl start wazuh-agent
```

### CentOS / Red Hat

```bash
# Adicionar repositorio RPM
rpm --import https://packages.wazuh.com/key/GPG-KEY-WAZUH

cat > /etc/yum.repos.d/wazuh.repo << EOF
[wazuh]
gpgcheck=1
gpgkey=https://packages.wazuh.com/key/GPG-KEY-WAZUH
enabled=1
name=EL-\$releasever - Wazuh
baseurl=https://packages.wazuh.com/4.x/yum/
protect=1
EOF

# Instalar
WAZUH_MANAGER="<IP_DO_MANAGER>" \
WAZUH_REGISTRATION_PASSWORD="<SENHA_DO_AUTHD>" \
WAZUH_AGENT_GROUP="linux" \
yum install wazuh-agent

systemctl daemon-reload
systemctl enable wazuh-agent
systemctl start wazuh-agent
```

---

## Verificar o agente no Manager

Apos a instalacao, confirme que o agente aparece no Wazuh Dashboard:

1. Acesse **Agents management > Summary**
2. O agente deve aparecer com status **Active**

Ou via linha de comando no Manager:

```bash
/var/ossec/bin/agent_control -l
```

---

## Expandindo a visibilidade — Sysmon (Windows)

O **Sysmon** (System Monitor) e uma ferramenta gratuita da Microsoft que registra atividades detalhadas do sistema no Event Viewer. Com o Sysmon, o Wazuh passa a receber eventos muito mais ricos, como:

| Event ID | O que captura |
|---|---|
| **1** | Criacao de processos (com linha de comando completa e hash) |
| **3** | Conexoes de rede |
| **7** | Carregamento de modulos/DLLs |
| **10** | Acesso a processos (ex: LSASS dump) |
| **11** | Criacao de arquivos |
| **13** | Modificacao de chaves de registro |
| **22** | Consultas DNS |
| **23** | Exclusao de arquivos |

### Etapa 1 — Baixar o Sysmon e a configuracao

```powershell
# Baixar o Sysmon da Microsoft Sysinternals
Invoke-WebRequest -Uri "https://download.sysinternals.com/files/Sysmon.zip" -OutFile "C:\Sysmon\Sysmon.zip"
Expand-Archive -Path "C:\Sysmon\Sysmon.zip" -DestinationPath "C:\Sysmon\"

# Baixar a configuracao modular (recomendada — cobre a maioria das tecnicas MITRE ATT&CK)
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/olafhartong/sysmon-modular/master/sysmonconfig.xml" -OutFile "C:\Sysmon\sysmonconfig.xml"
```

> A configuracao do [sysmon-modular](https://github.com/olafhartong/sysmon-modular) e amplamente usada pela comunidade de segurança e cobre a maioria das tecnicas do framework MITRE ATT&CK.

### Etapa 2 — Instalar o Sysmon

```powershell
cd C:\Sysmon
.\Sysmon64.exe -accepteula -i sysmonconfig.xml
```

> Use `Sysmon.exe` em sistemas de 32 bits.

### Etapa 3 — Instruir o Wazuh Agent a coletar os logs do Sysmon

Edite o arquivo de configuracao do agente:

```
C:\Program Files (x86)\ossec-agent\ossec.conf
```

Adicione dentro de `<ossec_config>`:

```xml
<!-- Sysmon -->
<localfile>
  <location>Microsoft-Windows-Sysmon/Operational</location>
  <log_format>eventchannel</log_format>
</localfile>
```

> Essa configuracao tambem pode (e deve) ser feita de forma centralizada via `agent.conf` no grupo `windows` do Wazuh Manager, conforme configurado na aula 03, sem precisar editar cada endpoint individualmente.

### Etapa 4 — Reiniciar o agente

```powershell
Restart-Service wazuhsvc
```

---

## Expandindo a visibilidade — Packetbeat

O **Packetbeat** e um agente da Elastic que captura e analisa trafico de rede em tempo real diretamente no endpoint. Ele decodifica protocolos como DNS, HTTP, TLS, MySQL, etc., e gera eventos em formato JSON.

Isso nos permite detectar, por exemplo:
- Comunicacoes com dominios de C2 (Command and Control)
- Exfiltracao de dados via HTTP
- Resolucoes DNS suspeitas

### Etapa 1 — Instalar o Packetbeat

**Linux:**

```bash
# Debian/Ubuntu
curl -L -O https://artifacts.elastic.co/downloads/beats/packetbeat/packetbeat-8.x.x-amd64.deb
dpkg -i packetbeat-8.x.x-amd64.deb
```

**Windows:**

Baixe o instalador em [elastic.co/downloads/beats/packetbeat](https://www.elastic.co/downloads/beats/packetbeat) e instale via PowerShell como administrador.

### Etapa 2 — Configurar o Packetbeat para gravar em arquivo JSON

Edite o arquivo `/etc/packetbeat/packetbeat.yml` (Linux) ou `C:\ProgramData\Elastic\Beats\packetbeat\packetbeat.yml` (Windows):

```yaml
# Desabilitar output para Elasticsearch (nao vamos usar aqui)
# output.elasticsearch:
#   hosts: ["localhost:9200"]

# Habilitar output para arquivo
output.file:
  path: "/var/log/packetbeat"
  filename: packetbeat.json
  rotate_every_kb: 10000
  number_of_files: 7
  codec.format:
    string: '%{[message]}'
```

**Windows** — altere o `path` para:

```yaml
output.file:
  path: "C:\\ProgramData\\packetbeat\\logs"
  filename: packetbeat.json
```

### Etapa 3 — Configurar protocolos a monitorar

No mesmo `packetbeat.yml`, ajuste a secao `protocols`:

```yaml
packetbeat.protocols:
  - type: dns
    ports: [53]
    include_authorities: true
    include_additionals: true

  - type: http
    ports: [80, 8080, 8000, 5000, 8002]

  - type: tls
    ports: [443, 8443]
```

### Etapa 4 — Iniciar o Packetbeat

```bash
# Linux
systemctl enable packetbeat
systemctl start packetbeat
```

```powershell
# Windows
Start-Service packetbeat
```

### Etapa 5 — Configurar o Wazuh Agent para coletar os logs do Packetbeat

**Linux** — adicione ao `ossec.conf` do agente (ou ao `agent.conf` do grupo no Manager):

```xml
<!-- Packetbeat -->
<localfile>
  <log_format>json</log_format>
  <location>/var/log/packetbeat/packetbeat.json</location>
</localfile>
```

**Windows** — adicione ao `ossec.conf`:

```xml
<!-- Packetbeat -->
<localfile>
  <log_format>json</log_format>
  <location>C:\ProgramData\packetbeat\logs\packetbeat.json</location>
</localfile>
```

Reinicie o agente apos a alteracao:

```bash
# Linux
systemctl restart wazuh-agent

# Windows (PowerShell)
Restart-Service wazuhsvc
```

---

## Verificacao final

### Confirmar que logs chegam ao Manager

```bash
# No Manager, acompanhar os alertas em tempo real
tail -f /var/ossec/logs/alerts/alerts.json
```

Se os logs do Sysmon e do Packetbeat aparecerem nesse arquivo, o pipeline esta funcionando.

### Verificar no Graylog

Acesse o Graylog em `http://<IP_DO_GRAYLOG>:9000` e va em **Search**. Filtre pelo nome do agente e confirme que os eventos chegam com os campos do Sysmon (`winlog.event_id`, `winlog.channel`) e do Packetbeat (`type`, `dns.question.name`, `http.request.method`).

---

## Resumo dos arquivos de configuracao importantes

| Sistema | Arquivo | Descricao |
|---|---|---|
| Windows | `C:\Program Files (x86)\ossec-agent\ossec.conf` | Configuracao local do agente |
| Linux | `/var/ossec/etc/ossec.conf` | Configuracao local do agente |
| Manager | `/var/ossec/etc/shared/windows/agent.conf` | Configuracao centralizada para o grupo Windows |
| Manager | `/var/ossec/etc/shared/linux/agent.conf` | Configuracao centralizada para o grupo Linux |
| Manager | `/var/ossec/logs/ossec.log` | Log de operacao do Manager (verificar erros de registro) |
| Manager | `/var/ossec/logs/alerts/alerts.json` | Alertas gerados — o que o Fluent-Bit le |

---

## Portas necessarias nos endpoints

| Porta | Protocolo | Descricao |
|---|---|---|
| 1514 | TCP | Envio de eventos do agente para o Manager |
| 1515 | TCP | Registro inicial do agente no Manager (authd) |

> O firewall do endpoint precisa permitir conexoes de saida para o IP do Manager nessas portas.

---

*<- Etapa anterior: [Wazuh Manager (Log Analysis)](03_wazuh_manager.md)*
*Proximo passo: Integracao com Threat Intelligence (OpenCTI / MISP)*
