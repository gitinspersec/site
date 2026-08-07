# Fundamentos de Cibersegurança

*⏱️ Tempo de leitura: ~5 min*

> Antes de aprender a **atacar**, você precisa entender *o que* se ataca, *por que* e *quem* faz isso. Essa é a base de todo o resto do curso.

## Pré-requisitos

- Nenhum conhecimento técnico prévio
- Um computador com acesso à internet
- Vontade de quebrar coisas (de forma ética)

## O que é cibersegurança?

Cibersegurança é a prática de **proteger sistemas, redes e dados** de acessos e usos não autorizados.

No fundo, tudo gira em torno de três garantias — a **tríade CIA**:

- **Confidencialidade** — só quem pode ver, vê *(sua senha não é pública)*
- **Integridade** — a informação não foi adulterada *(seu saldo no banco é o real)*
- **Disponibilidade** — o sistema está no ar quando você precisa *(o site não caiu)*

Todo ataque, no fim, quebra **pelo menos uma** dessas três. Guarde isso: é a lente pra enxergar qualquer problema de segurança.

## Dado, informação e ativo

Três palavras que parecem sinônimas — e não são:

- **Dado** — um fato bruto: `1998`, `maria@email.com`
- **Informação** — dado com contexto: *"a Maria nasceu em 1998"*
- **Ativo digital** — algo que tem **valor** e você quer proteger: uma conta, um banco de dados, um servidor, uma chave de API

> 💡 O trabalho do atacante é transformar **dado exposto** em **informação útil** pra chegar num **ativo valioso**. Um e-mail vazado (dado) vira alvo de phishing (informação) que rouba uma conta de admin (ativo).

## Onde a cibersegurança se aplica?

Em praticamente tudo que é digital:

- **Web e apps** — sites, APIs, app do banco
- **Redes** — Wi-Fi, servidores, infraestrutura
- **Nuvem** — AWS, GCP, Azure
- **Dispositivos** — celular, IoT, carro conectado
- **Pessoas** — o elo mais fraco, e o mais atacado

## Quem ataca — e por quê

"Hacker" não é uma coisa só. Os perfis mais comuns:

| Perfil | Motivação |
|--------|-----------|
| Script kiddie | Curiosidade e status; usa ferramentas prontas |
| Cibercriminoso | Dinheiro: ransomware, fraude, venda de dados |
| Hacktivista | Ideologia, protesto |
| Insider | Vingança ou suborno; já está dentro |
| APT / Estado | Espionagem e sabotagem; muito recurso e paciência |

O que quase todos procuram é o mesmo: **dados** (pra vender ou usar), **acesso** (pra escalar) e, direta ou indiretamente, **dinheiro**.

## O olhar de segurança

Segurança não é decorar ataques — é um **jeito de olhar**. Diante de qualquer sistema, o atacante faz três perguntas:

1. O que aqui tem **valor**? *(o ativo)*
2. Onde isso pode **falhar**? *(a superfície de ataque)*
3. Por onde eu **entraria**? *(o vetor)*

Você vai treinar esse olhar o curso inteiro. É o que separa quem roda um exploit pronto de sabe o que está fazendo.

## Red Team x Blue Team

- **Red Team** *(você, aqui)* — pensa como atacante. Encontra e explora falhas **antes** que um criminoso encontre.
- **Blue Team** — defende. Monitora, detecta e responde a ataques.

## Um mapa das vulnerabilidades

A maioria dos ataques cai em poucas categorias — e cada uma vira uma aula deste curso:

- **Autenticação fraca** — senhas ruins, hash quebrável → *Aula 2*
- **Criptografia mal usada** — dado sensível mal protegido → *Aula 2*
- **Configuração errada** — portas e serviços expostos, permissões largas → *Aulas 3 e 4*
- **Fator humano** — phishing, engenharia social → *Aula 5*
- **Software vulnerável** — versões antigas com falhas conhecidas (CVE) → *Aula 6*
- **Injeção** — SQL, XSS: fazer o sistema executar o que você mandou → *Aulas 6 e 7*

Não precisa entender nenhuma agora. É só o mapa da viagem.

## Próximos passos

A próxima página, [**Linux Básico**](linux.md), começa a parte prática no terminal.
