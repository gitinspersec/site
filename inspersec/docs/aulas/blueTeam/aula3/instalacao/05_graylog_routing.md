# Configuracao — Graylog Log Routing (Streams e Index Sets)

> **Posicao no stack:** Quinta etapa. Com o Wazuh Manager enviando alertas ao Graylog via Fluent-Bit, agora precisamos configurar o Graylog para **organizar, rotear e armazenar** esses logs de forma inteligente no Wazuh Indexer atraves de Streams e Index Sets dedicados.

**Referencias:**
- [Documentacao oficial Graylog — Streams](https://go2docs.graylog.org/current/making_sense_of_your_log_data/streams.htm)
- [Documentacao oficial Graylog — Index Sets](https://go2docs.graylog.org/current/setting_up_graylog/index_model.html)
- [Video da aula](https://www.youtube.com/watch?v=ZDL4MUxtIrY)
- [Post da aula — SOCFortress](https://socfortress.medium.com/part-5-intelligent-siem-logging-4d0656c0da5b)

---

## O problema sem roteamento

Quando o Graylog recebe os logs do Fluent-Bit, por padrao todos os dados caem no **stream "All messages"** e sao gravados em um unico indice generico. Isso gera varios problemas operacionais e de arquitetura:

- **Falta de retencao granular:** Nao e possivel aplicar politicas de retencao distintas por criticidade ou tipo de log.
- **Armazenamento desorganizado:** Logs de diferentes fontes e ambientes se misturam no mesmo espaco em disco.
- **Performance de busca reduzida:** Consultas precisam varrer indices maiores e desnecessarios.
- **Impossibilidade de multi-tenancy:** Nao ha como segregar dados por cliente, setor ou nivel de acesso.

A solucao e implementar o roteamento atraves de **Index Sets** e **Streams** com **Static Fields**.

---

## Fluxo de dados do roteamento

```
Fluent-Bit (Alertas do Wazuh)
        |
        | TCP porta 5555
        v
Graylog Input ("WAZUH EVENTS FLUENT BIT")
        |
        | (adiciona Static Field: log_type = wazuh)
        v
Stream "Wazuh Alerts"
        |
        | (Stream Rule: log_type match exactly "wazuh")
        | (Remove matches from 'All messages')
        v
Index Set "Wazuh Alerts"
        |
        v
Wazuh Indexer (OpenSearch)
```

| Componente | Funcao |
|---|---|
| **Input + Static Field** | Recebe os logs brutos e injeta uma etiqueta fixa (`log_type: wazuh`) para identificar a origem das mensagens |
| **Stream** | Avalia as mensagens em tempo real e as filtra com base em regras (Stream Rules), direcionando-as ao Index Set correto |
| **Index Set** | Define as propriedades fisicas de gravacao no Wazuh Indexer: prefixo do indice, numero de shards, replicas e politica de retencao |

---

## Passo 1 — Criar o Index Set

O Index Set define como e onde os dados serao persistidos no backend do Wazuh Indexer. Criaremos um conjunto dedicado exclusivamente aos alertas do Wazuh.

### Procedimento no Graylog WebUI

1. Navegue ate **System > Indices**
2. Clique no botao **Create index set**
3. Preencha as configuracoes basicas:

| Campo | Valor recomendado | Descricao |
|---|---|---|
| **Title** | `Wazuh Alerts` | Nome de identificacao na interface web |
| **Description** | `Alertas do Wazuh Manager` | Descricao informativa do conjunto |
| **Index prefix** | `wazuh_alerts` | Prefixo usado para nomear os indices no OpenSearch (ex: `wazuh_alerts_0`) |
| **Shards** | `1` | Quantidade de particoes primarias (1 e adequado para single-node) |
| **Replicas** | `0` | Numero de copias de seguranca (0 para single-node; 1+ para cluster) |
| **Rotation strategy** | `Index Size` | Criterio para fechar o indice atual e abrir um novo |
| **Retention strategy** | `Delete Index` | Acao executada ao atingir o limite maximo de indices mantidos |

4. Clique em **Save** para salvar o Index Set

> **Nota sobre armazenamento:** Ao usar `Index Size` na estrategia de rotacao, o Graylog rotaciona o indice quando ele atinge o tamanho maximo especificado, garantindo previsibilidade no consumo de disco.

---

## Passo 2 — Adicionar Static Field no Input

Para que o Graylog consiga identificar com precisao quais mensagens pertencem ao Wazuh e devem ser roteadas para o novo stream, adicionamos um **campo estatico** diretamente no input de entrada.

Dessa forma, toda mensagem recebida atraves dessa porta TCP recebera o campo automaticamente.

### Procedimento no Graylog WebUI

1. Navegue ate **System > Inputs**
2. Localize o input configurado para o Wazuh (ex: `WAZUH EVENTS FLUENT BIT` na porta TCP 5555)
3. Clique no menu **More Actions** ao lado do input
4. Selecione a opcao **Add static field**
5. Preencha os campos:
   - **Field name:** `log_type`
   - **Value:** `wazuh`
6. Clique em **Save**

A partir deste momento, qualquer novo evento que entrar pelo Fluent-Bit contera a chave `log_type` com o valor `wazuh`.

---

## Passo 3 — Criar o Stream

O Stream e responsavel por capturar as mensagens filtradas e associa-las ao Index Set criado no Passo 1.

### Procedimento no Graylog WebUI

1. Navegue ate a aba **Streams**
2. Clique no botao **Create stream**
3. Preencha os dados:

| Campo | Valor | Observacao |
|---|---|---|
| **Title** | `Wazuh Alerts` | Nome do stream |
| **Description** | `Logs de alertas recebidos do Wazuh Manager via Fluent-Bit` | Descricao funcional |
| **Index set** | `Wazuh Alerts` | Selecione o Index Set criado no Passo 1 |
| **Remove matches from 'All messages' stream** | **Habilitado (marcado)** | Remove o evento do stream padrao para evitar duplicidade |

> **Importante:** A opcao **"Remove matches from 'All messages' stream"** e fundamental. Sem ela ativada, cada log seria gravado duas vezes no disco (uma vez no indice `wazuh_alerts` e outra no indice padrao do Graylog).

4. Clique em **Save**

---

## Passo 4 — Configurar Stream Rules e Ativar o Stream

Com o stream criado, definimos a regra de correspondencia para filtrar os eventos etiquetados.

### Procedimento no Graylog WebUI

1. Localize o stream `Wazuh Alerts` recem-criado na lista de Streams
2. Clique no botao **Manage rules**
3. Clique em **Add stream rule**
4. Configure a regra de filtro:

| Campo de configuracao | Valor | Finalidade |
|---|---|---|
| **Field** | `log_type` | Campo injetado pelo input no Passo 2 |
| **Type** | `match exactly` | Exige correspondencia exata da string |
| **Value** | `wazuh` | Valor esperado no campo |

5. Clique em **Save** para salvar a regra
6. Clique em **I am done, activate stream** (ou no botao **Start stream**) para iniciar o processamento do stream


---

## Resumo dos componentes configurados

| Componente | Nome configurado | Detalhe |
|---|---|---|
| **Input Static Field** | `log_type = wazuh` | Injetado em `System > Inputs > More Actions` |
| **Stream** | `Wazuh Alerts` | Vinculado ao Index Set dedicado e isolado do "All messages" |
| **Stream Rule** | `log_type` match exactly `wazuh` | Regra de encaminhamento do stream |
| **Index Set** | `Wazuh Alerts` (prefixo: `wazuh_alerts`) | Politica de armazenamento e rotacao no storage |


