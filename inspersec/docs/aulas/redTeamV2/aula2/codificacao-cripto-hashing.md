# Codificação, Criptografia e Hashing

*⏱️ Tempo de leitura: ~8 min*

> Três palavras que quase todo mundo troca. Parecem a mesma coisa, mas resolvem problemas diferentes.

## Pré-requisitos

- Ter lido a [Aula 1](../aula1/fundamentos.md) (Fundamentos + Linux Básico)
- Terminal do Kali à mão pra testar os exemplos

## Transformar dados: por quê?

A gente transforma dados o tempo todo, por três motivos diferentes:

- Pra **transportar** sem quebrar (mandar um arquivo binário dentro de um texto, por exemplo)
- Pra **esconder** de quem não deveria ler
- Pra **verificar** se não foram alterados

Cada objetivo tem a sua ferramenta. Usar a errada pro objetivo é um erro comum de segurança.

## A diferença, de uma vez

|  | Codificação | Criptografia | Hashing |
|--|-------------|--------------|---------|
| **Objetivo** | formato / transporte | sigilo | verificar integridade |
| **Usa chave?** | não | sim | não |
| **Dá pra reverter?** | sim, qualquer um | sim, com a chave | não |
| **Exemplos** | Base64, Hex, URL | AES, RSA | MD5, SHA-256 |

O resto da página detalha cada coluna.

## Codificação

Codificar é trocar a **representação** de um dado pra ele caber ou trafegar num formato específico. Não tem chave e não tem segredo: qualquer pessoa reverte.

Exemplo em Base64:

- Texto: `InsperSec`
- Base64: `SW5zcGVyU2Vj`

Reverter é trivial — no terminal:

```bash
echo -n InsperSec | base64          # codifica → SW5zcGVyU2Vj
echo -n SW5zcGVyU2Vj | base64 -d    # reverte  → InsperSec
```

Outros exemplos comuns: Hexadecimal, URL-encoding (`%20` no lugar do espaço) e ASCII.

> ⚠️ Codificação **não protege nada**. Se você vê Base64, trate como texto aberto — é só uma fantasia. Nunca tente "esconder" uma senha em Base64 achando que é seguro; não é.

> 💡 O [CyberChef](https://gchq.github.io/CyberChef/) é ótimo pra brincar com isso no navegador: arraste a operação "From Base64" e cole o texto. Ele decodifica na hora — e a operação "Magic" tenta adivinhar sozinho qual foi a codificação usada.

## Criptografia

Criptografar é embaralhar um dado com uma **chave**, de um jeito que só quem tem a chave volta ao original. Sem a chave, é ruído.

A ideia é antiga. A **cifra de César** deslocava cada letra um número fixo de posições no alfabeto — a chave era esse número. É fácil de quebrar hoje, mas o princípio é o mesmo: transformar usando um segredo.

A criptografia moderna se divide em duas famílias:

- **Simétrica** — a mesma chave cifra e decifra. É rápida, boa pra grandes volumes. Ex.: **AES** (disco criptografado, ZIP com senha).
- **Assimétrica** — um par de chaves: a **pública** cifra, a **privada** decifra. Resolve o problema de combinar uma chave com alguém à distância. Ex.: **RSA**, **ECC**.

Você usa isso todo dia sem perceber: o cadeado do **HTTPS**, o **WhatsApp** (ponta a ponta), o login do banco — e o **SSH** que você usou pra entrar no Bandit.

> 💡 Na prática, HTTPS e SSH usam as duas famílias juntas: a assimétrica pra combinar uma chave secreta com segurança e a simétrica (bem mais rápida) pra transmitir os dados dali em diante.

## Hashing

Um hash é uma função de **mão única**: entra um dado de qualquer tamanho, sai um resumo de tamanho fixo. Não dá pra voltar.

- `InsperSec` → MD5 → `db2c4da6eb7cf3d66fe2323d4a4390e6`

As propriedades que importam:

- **Determinístico** — a mesma entrada dá sempre o mesmo hash.
- **Tamanho fixo** — tanto faz se a entrada tem 1 letra ou 1 GB.
- **Efeito avalanche** — mudar 1 caractere muda o hash inteiro:

| Texto | MD5 |
|-------|-----|
| `InsperSec` | `db2c4da6eb7cf3d66fe2323d4a4390e6` |
| `InsperSel` | `e5292fb1060bf47e6d163ec47482f150` |

Pra que serve: 

1. **verificar integridade:** se o hash do arquivo que você baixou "bate" com o do site, ele não foi corrompido no caminho.

2. **guardar senhas:** o sistema não guarda a sua senha (pelo menos não deve guardar), guarda o hash dela. No login, ele compara os hashes.

MD5 e SHA-1 já são considerados **quebrados**; o padrão atual é o **SHA-256**.

> 💡 Teste no CyberChef: jogue a operação "MD5" (ou "SHA2") numa palavra e mude uma letra pra ver a avalanche acontecer.

## Rainbow tables e salt

Se o hash não tem volta, como um atacante descobre a senha por trás dele?

Ele não reverte o hash. Em vez disso, **pré-calcula**: uma **rainbow table** é uma tabela gigante de senha → hash já pronta. O atacante pega o hash vazado e procura na tabela. Funciona justamente porque o hash é determinístico — `senha123` sempre vira `e7d80ffeefa212b7c5c55700e4f7193e`.

Senhas comuns já têm o hash conhecido. Uma rainbow table é literalmente uma lista assim, só que com bilhões de linhas:

| Senha | MD5 |
|-------|-----|
| `123456` | `e10adc3949ba59abbe56e057f20f883e` |
| `password` | `5f4dcc3b5aa765d61d8327deb882cf99` |
| `qwerty` | `d8578edf8458ce06fbc5bb76a58c5ca4` |
| `abc123` | `e99a18c428cb38d5f260853678922e03` |
| `admin` | `21232f297a57a5a743894a0e4a801fc3` |
| `football` | `37b4e2d82900d5e94b8da524fbeb33c0` |

Se a sua senha está numa lista dessas, o hash não protege nada — o atacante acha o original na hora.

A defesa é o **salt**: um valor aleatório somado à senha antes de calcular o hash. Cada usuário tem um salt diferente, então:

- a mesma senha vira hashes diferentes para pessoas diferentes;
- a rainbow table pré-calculada não serve pra nada.

Por isso uma senha bem guardada é sempre **hash + salt**, com algoritmos feitos pra isso, como o <a href="https://bcrypt-generator.com/" target="_blank" rel="noopener noreferrer">bcrypt</a>.

> 💡 Teste enquanto lê: gere o hash de uma senha no <a href="https://bcrypt-generator.com/" target="_blank" rel="noopener noreferrer">bcrypt-generator</a> e gere de novo. O resultado muda toda vez — é o salt aleatório embutido. Duas pessoas com a mesma senha acabam com hashes diferentes.

## Próximos passos

A próxima página, **Autenticação**, mostra como os sistemas usam tudo isso pra saber quem é você — senhas, tokens JWT, cookies e sessão.
