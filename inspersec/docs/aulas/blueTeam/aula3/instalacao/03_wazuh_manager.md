# Instalacao — Wazuh Manager (EDR / Log Analysis)

> **Posicao no stack:** Terceira etapa. O Wazuh Manager recebe os logs coletados pelos agentes nos endpoints, analisa com suas regras de deteccao e escreve os alertas no arquivo `alerts.json`. O Fluent-Bit entao envia esses alertas para o Graylog.

**Referencias:**
- [Documentacao oficial Wazuh Manager](https://documentation.wazuh.com/current/getting-started/components/wazuh-manager.html)
- [Documentacao de Active Response](https://documentation.wazuh.com/current/user-manual/capabilities/active-response/how-it-works.html)
- [API REST do Wazuh](https://documentation.wazuh.com/current/user-manual/api/reference.html)
- [Regras avancadas do SOCFortress](https://github.com/socfortress/Wazuh-Rules)
- [Video da aula](https://www.youtube.com/watch?v=nM-h-R293tk&list=PLB6hQ_WpB6U0WeroZAfssgRpxW8olnkqy&index=4)

---

## O que e um EDR?

**EDR** significa **Endpoint Detection and Response** (Deteccao e Resposta em Endpoints).

Um EDR e composto por duas pecas principais:

| Componente | Funcao |
|---|---|
| **Endpoint Agent** | Instalado em cada maquina, coleta eventos localmente |
| **Collection Manager** | Servidor central que recebe os eventos e analisa em busca de atividade maliciosa |

Sem um EDR, voce teria que logar em cada endpoint individualmente para investigar o que aconteceu — totalmente inviavel em escala. Com o EDR, tudo chega ao manager central.

---

## Por que não basta ter Antivirus?

O antivirus foi projetado para identificar malware conhecido. Porem:

- Atores de ameaca estao cada vez mais sofisticados
- Tecnicas como **fileless malware** (malware que nao grava arquivos em disco) passam completamente despercebidas pelo AV
- O AV nao grava o que aconteceu — apenas bloqueia (ou nao)

O EDR permite **gravar e analisar toda a atividade** do endpoint, incluindo:

- Conexoes de rede e consultas DNS
- Comandos executados
- Logins de usuarios
- Execucao de processos e subprocessos (ex: PowerShell sendo spawnado por Word)
- E muito mais

---

## Por que Wazuh?

O Wazuh e o melhor EDR open source disponivel. Suporta os principais sistemas operacionais e oferece nativamente:

| Capacidade | Descricao |
|---|---|
| **Log Data Analysis** | Analisa logs de Event Viewer, Syslog, JSON e formatos customizados |
| **File Integrity Monitoring (FIM)** | Detecta alteracoes em arquivos e diretorios criticos |
| **Vulnerability Detection** | Identifica CVEs nos pacotes instalados nos endpoints |
| **CIS Benchmark Assessment** | Avalia a conformidade com benchmarks de hardening |
| **Regulatory Compliance** | Mapeamento para PCI-DSS, HIPAA, GDPR, NIST, etc. |
| **Container Security** | Monitora atividade em containers Docker/Kubernetes |

Alem disso, o Wazuh permite criar regras de deteccao, decoders e integracoes customizadas — total flexibilidade para qualquer caso de uso.

---

## Fluxo de dados

```
Endpoint (Agente Wazuh)
        |
        | (logs coletados)
        v
Wazuh Manager
        |
        | (compara com rulesets; alertas sao gravados em)
        v
/var/ossec/logs/alerts/alerts.json
        |
        | (Fluent-Bit le o arquivo e encaminha)
        v
Graylog (Input TCP na porta 5555)
        |
        v
Wazuh Indexer (armazenamento)
```

---

## Instalacao

### Pre-requisitos

O repositorio do Wazuh ja deve ter sido adicionado na etapa anterior. Caso nao tenha sido, adicione:

```bash
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | gpg --no-default-keyring --keyring gnupg-ring:/usr/share/keyrings/wazuh.gpg --import && chmod 644 /usr/share/keyrings/wazuh.gpg

echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | tee -a /etc/apt/sources.list.d/wazuh.list

apt-get update
```

---

### Etapa 1 — Instalar o Wazuh Manager

```bash
apt-get -y install wazuh-manager
systemctl daemon-reload
systemctl enable wazuh-manager
systemctl start wazuh-manager
```

---

## Configurar o Graylog para receber os alertas

Antes de configurar o Fluent-Bit, precisamos abrir um input no Graylog para receber os dados.

### No Graylog WebUI

1. Acesse **System -> Inputs**
2. Selecione o tipo **Raw/Plaintext TCP**
3. Clique em **Launch new input**
4. Deixe as configuracoes padrao (porta `5555`) e salve

O Graylog agora esta ouvindo conexoes TCP na porta `5555`.

---

## Instalar e Configurar o Fluent-Bit

O **Fluent-Bit** e um agente leve de coleta de logs. Ele vai ler o arquivo `alerts.json` gerado pelo Wazuh Manager e encaminhar cada entrada para o input do Graylog.

### Etapa 2 — Instalar o Fluent-Bit

```bash
curl https://raw.githubusercontent.com/fluent/fluent-bit/master/install.sh | sh
```

### Etapa 3 — Configurar o Fluent-Bit

Edite o arquivo de configuracao principal:

```bash
nano /etc/fluent-bit/fluent-bit.conf
```

Substitua o conteudo pelo seguinte (ajuste o IP do Graylog):

```ini                                     
[SERVICE]
    flush        5
    daemon       Off
    log_level    info
    parsers_file parsers.conf
    plugins_file plugins.conf
    http_server  Off
    http_listen  0.0.0.0
    http_port    2020
    storage.metrics on
    storage.path /var/log/flb-storage/
    storage.sync normal
    storage.checksum off
    storage.backlog.mem_limit 5M
    Log_File /var/log/td-agent-bit.log
[INPUT]
    name  tail
    path  /var/ossec/logs/alerts/alerts.json
    tag wazuh
    parser  json
    Buffer_Max_Size 5MB
    Buffer_Chunk_Size 400k
    storage.type      filesystem
    Mem_Buf_Limit     512MB
[OUTPUT]
    Name            tcp
    Match           wazuh
    Host            <IP_DO_GRAYLOG>
    Port            5555
    net.keepalive   off
    net.connect_timeout 5
    Format json_lines
    json_date_key true



```

> Substitua `<IP_DO_GRAYLOG>` pelo IP real do servidor Graylog.

### Etapa 4 — Iniciar o Fluent-Bit

```bash
systemctl enable fluent-bit
systemctl start fluent-bit
```

---

## Configuracoes do Wazuh Manager

### Registrar Agentes com Senha (Autenticacao)

Por padrao, qualquer agente Wazuh pode se registrar no Manager. Para restringir isso, habilite autenticacao por senha.

#### 4.1 — Habilitar autenticacao por senha

Edite o arquivo de configuracao principal:

```bash
nano /var/ossec/etc/ossec.conf
```

Dentro da secao `<auth>`, adicione:

```xml
<auth>
  <use_password>yes</use_password>
</auth>
```

#### 4.2 — Definir a senha de registro

```bash
echo "<SENHA_CUSTOMIZADA>" > /var/ossec/etc/authd.pass
```

> Substitua `<SENHA_CUSTOMIZADA>` pela senha que voce escolheu. Essa senha sera exigida de todos os agentes ao se registrarem.

#### 4.3 — Ajustar permissoes do arquivo de senha

```bash
chmod 640 /var/ossec/etc/authd.pass
chown root:wazuh /var/ossec/etc/authd.pass
```

---

### Habilitar Deteccao de Vulnerabilidades

Para usar o Wazuh como scanner de vulnerabilidades nos endpoints, adicione (ou descomente) a seguinte secao no `/var/ossec/etc/ossec.conf`:

```xml
<vulnerability-detector>
  <enabled>yes</enabled>
  <interval>5m</interval>
  <min_full_scan_interval>6h</min_full_scan_interval>
  <run_on_start>yes</run_on_start>

  <!-- Feeds de vulnerabilidade -->
  <provider name="canonical">
    <enabled>yes</enabled>
    <os>bionic</os>
    <os>focal</os>
    <os>jammy</os>
    <update_interval>1h</update_interval>
  </provider>

  <provider name="debian">
    <enabled>yes</enabled>
    <os>buster</os>
    <os>bullseye</os>
    <update_interval>1h</update_interval>
  </provider>

  <provider name="redhat">
    <enabled>yes</enabled>
    <update_interval>1h</update_interval>
  </provider>

  <provider name="nvd">
    <enabled>yes</enabled>
    <update_interval>1h</update_interval>
  </provider>
</vulnerability-detector>
```

> Leitura adicional: [Documentacao de Vulnerability Detection](https://documentation.wazuh.com/current/user-manual/capabilities/vulnerability-detection/running-vu-scan.html)

---

### Configurar Grupos de Agentes

O Wazuh permite gerenciar a configuracao dos agentes a partir do Manager, sem precisar logar em cada endpoint, além disso as mudanças se mantém caso alteremos o manager geral. As configuracoes ficam nos **arquivos de grupo**, localizados em `/var/ossec/etc/shared/`.

A pratica recomendada e criar grupos separados por sistema operacional.

Para configurar os agentes especificos vamos adicionar dois novos "groups" dentro de managment na interface visual do wazuh

#### Grupo Linux (`/var/ossec/etc/shared/linux/agent.conf`)

```xml
<agent_config>
	<client_buffer>
		<!-- Agent buffer options -->
		<disabled>no</disabled>
		<queue_size>5000</queue_size>
		<events_per_second>500</events_per_second>
	</client_buffer>
	<!-- Policy monitoring -->
	<rootcheck>
		<disabled>no</disabled>
		<!-- Frequency that rootcheck is executed - every 12 hours -->
		<frequency>43200</frequency>
		<rootkit_files>/var/ossec/etc/shared/rootkit_files.txt</rootkit_files>
		<rootkit_trojans>/var/ossec/etc/shared/rootkit_trojans.txt</rootkit_trojans>
		<system_audit>/var/ossec/etc/shared/system_audit_rcl.txt</system_audit>
		<system_audit>/var/ossec/etc/shared/system_audit_ssh.txt</system_audit>
		<system_audit>/var/ossec/etc/shared/cis_debian_linux_rcl.txt</system_audit>
		<skip_nfs>yes</skip_nfs>
	</rootcheck>
	<wodle name="open-scap">
		<disabled>yes</disabled>
		<timeout>1800</timeout>
		<interval>1d</interval>
		<scan-on-start>yes</scan-on-start>
		<content type="xccdf" path="ssg-debian-8-ds.xml">
			<profile>xccdf_org.ssgproject.content_profile_common</profile>
		</content>
		<content type="oval" path="cve-debian-oval.xml"/>
	</wodle>
	<!-- File integrity monitoring -->
	<syscheck>
		<disabled>no</disabled>
		<!-- Frequency that syscheck is executed default every 12 hours -->
		<frequency>43200</frequency>
		<scan_on_start>yes</scan_on_start>
		<!-- Directories to check  (perform all possible verifications) -->
		<directories>/etc,/usr/bin,/usr/sbin</directories>
		<directories>/bin,/sbin,/boot</directories>
		<!-- Files/directories to ignore -->
		<ignore>/etc/mtab</ignore>
		<ignore>/etc/hosts.deny</ignore>
		<ignore>/etc/mail/statistics</ignore>
		<ignore>/etc/random-seed</ignore>
		<ignore>/etc/random.seed</ignore>
		<ignore>/etc/adjtime</ignore>
		<ignore>/etc/httpd/logs</ignore>
		<ignore>/etc/utmpx</ignore>
		<ignore>/etc/wtmpx</ignore>
		<ignore>/etc/cups/certs</ignore>
		<ignore>/etc/dumpdates</ignore>
		<ignore>/etc/svc/volatile</ignore>
		<ignore>/sys/kernel/security</ignore>
		<ignore>/sys/kernel/debug</ignore>
		<!-- File types to ignore -->
		<ignore type="sregex">.log$|.swp$</ignore>
		<!-- Check the file, but never compute the diff -->
		<nodiff>/etc/ssl/private.key</nodiff>
		<skip_nfs>yes</skip_nfs>
		<skip_dev>yes</skip_dev>
		<skip_proc>yes</skip_proc>
		<skip_sys>yes</skip_sys>
		<!-- Nice value for Syscheck process -->
		<process_priority>10</process_priority>
		<!-- Maximum output throughput -->
		<max_eps>100</max_eps>
		<!-- Database synchronization settings -->
		<synchronization>
			<enabled>yes</enabled>
			<interval>5m</interval>
			<response_timeout>30</response_timeout>
			<queue_size>16384</queue_size>
			<max_eps>10</max_eps>
		</synchronization>
	</syscheck>
	<!-- Log analysis -->
	<localfile>
		<log_format>syslog</log_format>
		<location>/var/ossec/logs/active-responses.log</location>
	</localfile>
	<localfile>
		<log_format>syslog</log_format>
		<location>/var/log/messages</location>
	</localfile>
	<localfile>
		<log_format>syslog</log_format>
		<location>/var/log/auth.log</location>
	</localfile>
	<localfile>
		<log_format>syslog</log_format>
		<location>/var/log/syslog</location>
	</localfile>
	<localfile>
		<log_format>command</log_format>
		<command>df -P</command>
		<frequency>360</frequency>
	</localfile>
	<localfile>
		<log_format>full_command</log_format>
		<command>netstat -tan |grep LISTEN |grep -v 127.0.0.1 | sort</command>
		<frequency>360</frequency>
	</localfile>
	<localfile>
		<log_format>full_command</log_format>
		<command>last -n 5</command>
		<frequency>360</frequency>
	</localfile>
	<wodle name="osquery">
		<disabled>yes</disabled>
		<run_daemon>yes</run_daemon>
		<log_path>/var/log/osquery/osqueryd.results.log</log_path>
		<config_path>/etc/osquery/osquery.conf</config_path>
		<add_labels>yes</add_labels>
	</wodle>
	<wodle name="syscollector">
		<disabled>no</disabled>
		<interval>24h</interval>
		<scan_on_start>yes</scan_on_start>
		<packages>yes</packages>
		<os>yes</os>
		<hotfixes>yes</hotfixes>
		<ports all="no">yes</ports>
		<processes>yes</processes>
	</wodle>
</agent_config>
```

#### Grupo Windows (`/var/ossec/etc/shared/windows/agent.conf`)

```xml
<agent_config>
	<client_buffer>
		<!-- Agent buffer options -->
		<disabled>no</disabled>
		<queue_size>5000</queue_size>
		<events_per_second>500</events_per_second>
	</client_buffer>
	<!-- Policy monitoring -->
	<rootcheck>
		<disabled>no</disabled>
		<windows_apps>./shared/win_applications_rcl.txt</windows_apps>
		<windows_malware>./shared/win_malware_rcl.txt</windows_malware>
	</rootcheck>
	<sca>
		<enabled>yes</enabled>
		<scan_on_start>yes</scan_on_start>
		<interval>12h</interval>
		<skip_nfs>yes</skip_nfs>
	</sca>
	<!-- File integrity monitoring -->
	<syscheck>
		<disabled>no</disabled>
		<!-- Frequency that syscheck is executed default every 12 hours -->
		<frequency>43200</frequency>
		<!-- Default files to be monitored. -->
		<directories recursion_level="0" restrict="regedit.exe$|system.ini$|win.ini$">%WINDIR%</directories>
		<directories recursion_level="0" restrict="at.exe$|attrib.exe$|cacls.exe$|cmd.exe$|eventcreate.exe$|ftp.exe$|lsass.exe$|net.exe$|net1.exe$|netsh.exe$|reg.exe$|regedt32.exe|regsvr32.exe|runas.exe|sc.exe|schtasks.exe|sethc.exe|subst.exe$">%WINDIR%\SysNative</directories>
		<directories recursion_level="0">%WINDIR%\SysNative\drivers\etc</directories>
		<directories recursion_level="0" restrict="WMIC.exe$">%WINDIR%\SysNative\wbem</directories>
		<directories recursion_level="0" restrict="powershell.exe$">%WINDIR%\SysNative\WindowsPowerShell\v1.0</directories>
		<directories recursion_level="0" restrict="winrm.vbs$">%WINDIR%\SysNative</directories>
		<!-- 32-bit programs. -->
		<directories recursion_level="0" restrict="at.exe$|attrib.exe$|cacls.exe$|cmd.exe$|eventcreate.exe$|ftp.exe$|lsass.exe$|net.exe$|net1.exe$|netsh.exe$|reg.exe$|regedit.exe$|regedt32.exe$|regsvr32.exe$|runas.exe$|sc.exe$|schtasks.exe$|sethc.exe$|subst.exe$">%WINDIR%\System32</directories>
		<directories recursion_level="0">%WINDIR%\System32\drivers\etc</directories>
		<directories recursion_level="0" restrict="WMIC.exe$">%WINDIR%\System32\wbem</directories>
		<directories recursion_level="0" restrict="powershell.exe$">%WINDIR%\System32\WindowsPowerShell\v1.0</directories>
		<directories recursion_level="0" restrict="winrm.vbs$">%WINDIR%\System32</directories>
		<directories realtime="yes">%PROGRAMDATA%\Microsoft\Windows\Start Menu\Programs\Startup</directories>
		<ignore>%PROGRAMDATA%\Microsoft\Windows\Start Menu\Programs\Startup\desktop.ini</ignore>
		<ignore type="sregex">.log$|.htm$|.jpg$|.png$|.chm$|.pnf$|.evtx$</ignore>
		<!-- Windows registry entries to monitor. -->
		<windows_registry>HKEY_LOCAL_MACHINE\Software\Classes\batfile</windows_registry>
		<windows_registry>HKEY_LOCAL_MACHINE\Software\Classes\cmdfile</windows_registry>
		<windows_registry>HKEY_LOCAL_MACHINE\Software\Classes\comfile</windows_registry>
		<windows_registry>HKEY_LOCAL_MACHINE\Software\Classes\exefile</windows_registry>
		<windows_registry>HKEY_LOCAL_MACHINE\Software\Classes\piffile</windows_registry>
		<windows_registry>HKEY_LOCAL_MACHINE\Software\Classes\AllFilesystemObjects</windows_registry>
		<windows_registry>HKEY_LOCAL_MACHINE\Software\Classes\Directory</windows_registry>
		<windows_registry>HKEY_LOCAL_MACHINE\Software\Classes\Folder</windows_registry>
		<windows_registry arch="both">HKEY_LOCAL_MACHINE\Software\Classes\Protocols</windows_registry>
		<windows_registry arch="both">HKEY_LOCAL_MACHINE\Software\Policies</windows_registry>
		<windows_registry>HKEY_LOCAL_MACHINE\Security</windows_registry>
		<windows_registry arch="both">HKEY_LOCAL_MACHINE\Software\Microsoft\Internet Explorer</windows_registry>
		<windows_registry>HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services</windows_registry>
		<windows_registry>HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDLLs</windows_registry>
		<windows_registry>HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\SecurePipeServers\winreg</windows_registry>
		<windows_registry arch="both">HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Run</windows_registry>
		<windows_registry arch="both">HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\RunOnce</windows_registry>
		<windows_registry>HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\RunOnceEx</windows_registry>
		<windows_registry arch="both">HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\URL</windows_registry>
		<windows_registry arch="both">HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies</windows_registry>
		<windows_registry arch="both">HKEY_LOCAL_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Windows</windows_registry>
		<windows_registry arch="both">HKEY_LOCAL_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Winlogon</windows_registry>
		<windows_registry arch="both">HKEY_LOCAL_MACHINE\Software\Microsoft\Active Setup\Installed Components</windows_registry>
		<!-- Windows registry entries to ignore. -->
		<registry_ignore>HKEY_LOCAL_MACHINE\Security\Policy\Secrets</registry_ignore>
		<registry_ignore>HKEY_LOCAL_MACHINE\Security\SAM\Domains\Account\Users</registry_ignore>
		<registry_ignore type="sregex">\Enum$</registry_ignore>
		<registry_ignore>HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\MpsSvc\Parameters\AppCs</registry_ignore>
		<registry_ignore>HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\MpsSvc\Parameters\PortKeywords\DHCP</registry_ignore>
		<registry_ignore>HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\MpsSvc\Parameters\PortKeywords\IPTLSIn</registry_ignore>
		<registry_ignore>HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\MpsSvc\Parameters\PortKeywords\IPTLSOut</registry_ignore>
		<registry_ignore>HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\MpsSvc\Parameters\PortKeywords\RPC-EPMap</registry_ignore>
		<registry_ignore>HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\MpsSvc\Parameters\PortKeywords\Teredo</registry_ignore>
		<registry_ignore>HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\PolicyAgent\Parameters\Cache</registry_ignore>
		<registry_ignore>HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\RunOnceEx</registry_ignore>
		<registry_ignore>HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\ADOVMPPackage\Final</registry_ignore>
		<!-- Frequency for ACL checking (seconds) -->
		<windows_audit_interval>60</windows_audit_interval>
		<!-- Nice value for Syscheck module -->
		<process_priority>10</process_priority>
		<!-- Maximum output throughput -->
		<max_eps>100</max_eps>
		<!-- Database synchronization settings -->
		<synchronization>
			<enabled>yes</enabled>
			<interval>5m</interval>
			<max_interval>1h</max_interval>
			<max_eps>10</max_eps>
		</synchronization>
	</syscheck>
	<!-- System inventory -->
	<wodle name="syscollector">
		<disabled>no</disabled>
		<interval>1h</interval>
		<scan_on_start>yes</scan_on_start>
		<hardware>yes</hardware>
		<os>yes</os>
		<network>yes</network>
		<packages>yes</packages>
		<ports all="no">yes</ports>
		<processes>yes</processes>
		<!-- Database synchronization settings -->
		<synchronization>
			<max_eps>10</max_eps>
		</synchronization>
	</wodle>
	<!-- CIS policies evaluation -->
	<wodle name="cis-cat">
		<disabled>yes</disabled>
		<timeout>1800</timeout>
		<interval>1d</interval>
		<scan-on-start>yes</scan-on-start>
		<java_path>\\server\jre\bin\java.exe</java_path>
		<ciscat_path>C:\cis-cat</ciscat_path>
	</wodle>
	<!-- Osquery integration -->
	<wodle name="osquery">
		<disabled>yes</disabled>
		<run_daemon>yes</run_daemon>
		<bin_path>C:\Program Files\osquery\osqueryd</bin_path>
		<log_path>C:\Program Files\osquery\log\osqueryd.results.log</log_path>
		<config_path>C:\Program Files\osquery\osquery.conf</config_path>
		<add_labels>yes</add_labels>
	</wodle>
	<!-- Active response -->
	<active-response>
		<disabled>no</disabled>
		<ca_store>wpk_root.pem</ca_store>
		<ca_verification>yes</ca_verification>
	</active-response>
	<!-- Log analysis -->
	<localfile>
		<location>Microsoft-Windows-Sysmon/Operational</location>
		<log_format>eventchannel</log_format>
	</localfile>
	<localfile>
		<location>Windows PowerShell</location>
		<log_format>eventchannel</log_format>
	</localfile>
	<localfile>
		<location>Microsoft-Windows-CodeIntegrity/Operational</location>
		<log_format>eventchannel</log_format>
	</localfile>
	<localfile>
		<location>Microsoft-Windows-TaskScheduler/Operational</location>
		<log_format>eventchannel</log_format>
	</localfile>
	<localfile>
		<location>Microsoft-Windows-PowerShell/Operational</location>
		<log_format>eventchannel</log_format>
	</localfile>
	<localfile>
		<location>Microsoft-Windows-Windows Firewall With Advanced Security/Firewall</location>
		<log_format>eventchannel</log_format>
	</localfile>
	<localfile>
		<location>Microsoft-Windows-Windows Defender/Operational</location>
		<log_format>eventchannel</log_format>
	</localfile>
</agent_config>
```

---

### Reiniciar o Wazuh Manager

Sempre que alterar o `ossec.conf` ou os arquivos de grupo, reinicie o servico:

```bash
systemctl restart wazuh-manager
```

---

## Adicionar Regras de Deteccao Avancadas

A equipe do **SOCFortress** mantem um repositorio publico com regras de deteccao avancadas para o Wazuh. Essas regras cobrem tecnicas do MITRE ATT&CK, deteccao de ferramentas ofensivas e muito mais.

**Repositorio:** [github.com/socfortress/Wazuh-Rules](https://github.com/socfortress/Wazuh-Rules)

### Etapa 5 — Baixar e aplicar as regras

Execute o script disponivel no repositorio para aplicar automaticamente as regras no seu Wazuh Manager:

```bash
curl -so ~/wazuh_socfortress_rules.sh https://raw.githubusercontent.com/socfortress/Wazuh-Rules/main/wazuh_socfortress_rules.sh && bash ~/wazuh_socfortress_rules.sh
```



Apos aplicar as regras:

```bash
systemctl restart wazuh-manager
```

---

## Verificar o funcionamento

### Verificar se o Manager esta rodando

```bash
systemctl status wazuh-manager
```

### Verificar se alertas estao sendo gerados

```bash
tail -f /var/ossec/logs/alerts/alerts.json
```

Se logs aparecerem em tempo real, o Manager esta recebendo eventos dos agentes e gerando alertas.

### Verificar se o Fluent-Bit esta enviando para o Graylog

```bash
systemctl status fluent-bit
```

No Graylog, acesse **Search** e verifique se mensagens estao chegando.

---

## Portas necessarias

| Porta | Protocolo | Descricao |
|---|---|---|
| 1514 | TCP/UDP | Comunicacao entre agentes e Manager |
| 1515 | TCP | Registro de novos agentes (authd) |
| 55000 | TCP | API REST do Wazuh Manager |
| 5555 | TCP | Input Raw/Plaintext do Graylog (Fluent-Bit -> Graylog) |

---

*<- Etapa anterior: [Graylog (Log Ingestion)](02_graylog.md)*
*Proximo passo: Wazuh Agents (instalacao nos endpoints)*
