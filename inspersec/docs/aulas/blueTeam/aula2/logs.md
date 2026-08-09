# Objetivos

Ao final da aula, os alunos deverão ser capazes de:

- compreender o papel dos logs em operações de segurança;
- identificar as principais fontes de logs em Windows e Linux;
- interpretar eventos básicos relacionados a autenticação, processos e alterações administrativas;
- entender o funcionamento e a arquitetura do Syslog;
- compreender as limitações dos logs nativos do Windows;
- entender como o Sysmon amplia a visibilidade sobre atividades do sistema;
- utilizar Event Viewer, Sysmon e ferramentas do Sysinternals para investigação inicial.


## 1. O que são Logs?


"Arquivos de log são arquivos gerados por software que contêm informações sobre as operações, as atividades e os padrões de uso de uma aplicação, servidor ou sistema de TI." [AWS-Amazon](https://aws.amazon.com/pt/what-is/log-files/)

São através dos logs que vemos o real funcionamente de uma aplicação. Neles podemos observar o histórico de processos, eventos do sistema, interaçoes com usuarios e mensagens descritivas de fluxos de operaçoes todos carimbados com data e hora do ocorrido, nos possibilitando a analise do sistema em funcionamento. 

Tome como exemplo o log abaixo.
```
2026-08-08 23:45:20
User: administrator
Source IP: 192.168.1.54
Event: Login failed 
Reason: Invalid Password
```
Nele podemos observar um usuário de nome "administrador" com o IP 192.168.1.54 tentou logar, mas forneceu a senha incorretamente. Isoladamente pode nao siginificar muito.

Mas imagine:
```
23:45:21 administrator LOGIN FAILED
23:45:21 administrator LOGIN FAILED
23:45:21 administrator LOGIN FAILED
... 20 ...
23:45:25 administrator LOGIN FAILED
23:45:25 administrator LOGIN FAILED
23:45:25 administrator LOGIN FAILED
```

Multiplas tentativas de login em tempos extremamente curtos podendo indicar um password guessing ou brute force da senha.

Aqui podemos fazer uma importante distinção, logs não são necessariamente um alerta, mas um importante indicador de uma possível falha ou comportamente indevido.

Já uma regra de detecção analisa tais casos e define o quanto tais comportamentos merecem atenção.


## 2. Por que Logs são importantes

Para o time de segurança Blue Team, logs são importantes principalmente para:

**Monitoramento**\
Observar continuamente o ambiente, as operações e as interações dos serviços e usuários.

**Detecção**\
Identificar comportamentos potencialmente maliciosos e falhas nos fluxos de serviços.

**Investigação e Forense**\
Reconstruir o fluxo e linha do tempo do que aconteceu após um alerta ou incidente.

**Threat Hunting**\
Pesquisar proativamente evidências de compromentimento ou ataque (IoC & IoA)


Uma investigação normalmenete busca responder as seguintes perguntas.
```
Quem executou?
​        ↓
O que foi executado?
        ↓
Quando aconteceu?
        ↓
Em qual máquina?
        ↓
De qual origem?
        ↓
O que aconteceu depois?
```

Logs diferentes respondem diferentes partes dessas perguntas.

## 3. Tipos de Logs

Podemos gerar e coletar logs em diversas partes da aplicação:

| Fonte               | Exemplos de informação         |
| ------------------- | ------------------------------ |
| Sistema operacional | login, processos, serviços     |
| Firewall            | conexões permitidas/bloqueadas |
| DNS                 | consultas de domínio           |
| Proxy               | acesso web                     |
| Aplicações          | erros e autenticação           |
| Active Directory    | usuários e grupos              |
| EDR                 | processos e comportamento      |
| VPN                 | acessos remotos                |
| Cloud               | alterações administrativas     |
| IDS/IPS             | tráfego suspeito               |


> IDS/IPS: Intrusion Detection System / Intrusion Prevention System, sistemas que se complementao detectando e previnindo operações maliciosas \
> EDR: Endpoint Detection and Response, ferramenta que monitora dispositivos de ponta como celular e computadores.


Isso nos introduz um problema: Como podemos analisar logs de centenas ou milhares de maquianas em todas essas camadas?

Imagine abrir cada um dos logs anteriores de cada computador que se comunicou com sua aplicação, parece impossivel certo?

>Spoiler: Para isso usamos ferramentas como o SIEM e SOEM, ferramentas dedicadas a classificar, agrupar e lidar com tais informaçoes. Discutiremos mais na proxima aula.


## 4. Retenção



