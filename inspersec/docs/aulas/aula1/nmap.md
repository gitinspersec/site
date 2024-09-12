# Introdução ao Nmap

O **Nmap** (Network Mapper) é uma ferramenta de código aberto usada para exploração de rede e auditorias de segurança. Ele permite descobrir hosts e serviços em uma rede, identificar portas abertas, detectar sistemas operacionais, versões de software, entre outras funcionalidades.

## Instalação do Nmap no Linux

Para instalar o Nmap, use o seguinte comando:

```bash
sudo apt install nmap
```

## Uso Básico do Nmap
O uso básico do Nmap segue a estrutura:

```bash
nmap [opções] [alvo]
```

### Opções Comuns

- **sS:** Realiza um scan SYN (conhecido como "half-open" scan).
- **sT:** Realiza um scan completo de TCP.
- **O:** Detecta o sistema operacional do host alvo.
- **A:** Executa a detecção de sistema operacional e serviços.
- **p** [porta]: Especifica a porta ou o intervalo de portas para escanear.
- **v:** Aumenta a verbosidade, exibindo mais detalhes sobre o scan.
- **oN [arquivo]:** Salva a saída do scan em um arquivo.
- **-p-:** Escaneia todas as portas (de 1 a 65535).
- **-sn:** Desativa o scan de portas, realizando apenas o scan de hosts.
- **--script:** Executa scripts NSE (Nmap Scripting Engine).


## Scripts do Nmap:

O Nmap tem suporte a NSE (Nmap Scripting Engine), que permite executar scripts personalizados para ampliar as funcionalidades da ferramenta. Dois desses scripts são html.header e html.title, que analisam servidores web e retornam informações sobre o conteúdo HTML.

### Scrpit html.header

O script html.header retorna os cabeçalhos HTTP de uma página. Esses cabeçalhos contêm metadados sobre a página web e são enviados pelo servidor junto com o conteúdo da página.

Para usar o script html.header, execute o seguinte comando:

```bash
nmap --script=http-headers [alvo]
```

### Script html.title

O script html.title retorna o título de uma página HTML. O título é uma tag HTML que define o título da página, exibido na aba do navegador.

Para usar o script html.title, execute o seguinte comando:

```bash
nmap --script=http-title [alvo]
```


## Exemplos de Uso

### Exemplo 1: Scan basico de um alvo

```bash
nmap 192.168.1.1
```

### Exemplo 2: Scan de portas específicas

```bash
nmap -p 22,80,443 192.168.1.1
```

### Exemplo 3: Scan completo de portas em um alvo

```bash
nmap -p- 192.168.1.1
```

### Exemplo 4: Detectar sistema operacional e versão dos serviços

```bash
nmap -A 192.168.1.1
```

### Exemplo 5: Escaneando uma rede inteira

```bash
nmap 192.168.1.0/24
```

### Exemplo 6: Salvar a saída do scan em um arquivo

```bash
nmap -oN resultado.txt 192.168.1.1
```


