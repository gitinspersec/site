# Criptomoedas e Segurança

*⏱️ Tempo de leitura: ~4 min*

> Material extra da Aula 2. Criptomoeda é, na prática, a criptografia e o hashing que você estudou — aplicados a dinheiro.

## Pré-requisitos

- Ter lido a Aula 2, principalmente [Codificação, Criptografia e Hashing](../aula2/codificacao-cripto-hashing.md)

## A ideia central

Uma criptomoeda como o Bitcoin não tem banco no meio. Não existe um servidor central dizendo "o fulano tem X reais". No lugar disso, um **livro-caixa público** (a *blockchain*) registra todas as transações, e a matemática da Aula 2 é o que impede a trapaça.

Duas peças fazem isso funcionar — e você já conhece as duas.

## Blockchain = hashing encadeado

A blockchain é uma corrente de **blocos**. Cada bloco guarda um monte de transações e, junto, **o hash do bloco anterior**.

```
[Bloco 1] ──hash──> [Bloco 2] ──hash──> [Bloco 3] ──> ...
```

Lembra do efeito avalanche da Aula 2? Se alguém altera uma transação num bloco antigo, o hash daquele bloco muda, o que quebra a referência no bloco seguinte, e no seguinte, e assim por diante. Fraudar um bloco exigiria refazer todos os que vêm depois dele ao mesmo tempo — inviável na prática. É a integridade por hashing, em escala gigante.

**Minerar** é procurar um número (o *nonce*) que faça o hash do bloco começar com uma certa quantidade de zeros. Não tem atalho: é força bruta de hash, tentativa após tentativa. É por isso que mineração "gasta energia".

## Carteira = chaves assimétricas

Sua carteira é um **par de chaves** — a mesma ideia de RSA/ECC da Aula 2:

- A **chave pública** vira o seu **endereço**: você divulga pra receber.
- A **chave privada** **assina** as transações: é ela que prova que foi você quem autorizou.

Quem tem a chave privada controla o dinheiro. Daí a frase **"not your keys, not your coins"**. E não existe "esqueci minha senha": perdeu a chave privada, perdeu tudo.

## Por que isso importa pra segurança

Cripto junta duas coisas que atraem atacante: dinheiro e irreversibilidade (transação confirmada não volta atrás). O resultado:

- **Ransomware** cobra o resgate em cripto — é o meio de pagamento do cibercrime (você vê ransomware na Aula 6).
- **Roubo de carteira**: phishing pedindo a *seed phrase*, apps de carteira falsos e malware que **troca o endereço copiado no clipboard** pelo do atacante na hora de você colar.
- **Golpes**: rug pull, tokens falsos e o "pig butchering" — engenharia social pura (Aula 5).
- **Exchanges** centralizadas viram alvo porque guardam as chaves de milhares de pessoas num lugar só.

> 💡 Repare no padrão: quase todo roubo de cripto é roubo da **chave privada** — por phishing ou malware —, não quebra da criptografia. A matemática é segura; o elo fraco é a proteção da chave. É a mesma lição da Aula 2.
