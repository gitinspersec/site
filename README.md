# InsperSec — Site

Repositório do site para membros do InsperSec.

## Onde editar o conteúdo do site

O conteúdo publicado fica em `inspersec/docs`. Exemplos:

- Blue Team: `inspersec/docs/aulas/blueTeam/`
- Red Team: `inspersec/docs/aulas/redTeam/`
- Instalação/Guias: `inspersec/docs/Instalação/`

O arquivo de configuração do MkDocs está em `inspersec/mkdocs.yml`.

## Como instalar MkDocs

Para instalar o MkDocs e o tema Material (usado neste projeto), execute o comando:

```bash
pip install mkdocs mkdocs-material
```

Certifique-se de ter o Python e o pip instalados no seu sistema.

## Como executar

Para executar o site localmente, navegue até a pasta `inspersec` e execute o comando:

```bash
mkdocs serve
```

Isso iniciará um servidor de desenvolvimento local em `http://localhost:8000`, onde você pode visualizar o site.

## Como fazer o deploy

Este projeto já está configurado para usar GitHub Pages. Para fazer o deploy do site, navegue até a pasta `inspersec` e execute o comando:

```bash
mkdocs gh-deploy
```

Isso construirá o site e fará o deploy automaticamente para o branch `gh-pages` do repositório GitHub.

