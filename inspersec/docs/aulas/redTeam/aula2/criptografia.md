# Criptografia

## Introdução

A criptografia é uma técnica usada para proteger a confidencialidade, integridade e autenticidade de informações. Ela envolve a transformação de dados em um formato ilegível, chamado de texto cifrado, que só pode ser decifrado por uma chave específica. A criptografia é amplamente utilizada em sistemas de segurança de informações, como comunicações seguras, armazenamento de dados confidenciais e autenticação de usuários.

## Antigamente

A criptografia tem sido usada ao longo da história para proteger informações confidenciais. Um dos métodos de criptografia mais antigos é a cifra de César, que foi usada pelo imperador romano Júlio César para enviar mensagens secretas a seus generais. A cifra de César é um tipo de cifra de substituição em que cada letra do texto original é substituída por uma letra deslocada um número fixo de posições no alfabeto.

Como funciona a cifra de César:

- **Texto Original:** `Entre no time de marketing`
- **Texto Cifrado:** `Hqshb qr wlrh gh pdulwlrj`

Neste exemplo, o texto original foi deslocado três posições no alfabeto para criar o texto cifrado. Para decifrar a mensagem, basta deslocar as letras três posições no sentido oposto.

Ou seja, shifitamos para o lado 3 vezes

| Original | Cifrado |
|----------|---------|
| A        | D       |
| B        | E       |
| C        | F       |
| D        | G       |
| E        | H       |

etc...

Mas essa técnica é muito simples e facilmente quebrada

## Criptografia Moderna

Atualmente, a criptografia moderna é baseada em algoritmos matemáticos complexos que garantem a segurança das informações. Existem dois tipos principais de criptografia:

- **Criptografia Simétrica:** Na criptografia simétrica, a mesma chave é usada para criptografar e descriptografar os dados. Isso significa que a chave de criptografia deve ser compartilhada entre o remetente e o destinatário. Exemplos de algoritmos de criptografia simétrica incluem o DES (Data Encryption Standard) e o AES (Advanced Encryption Standard).

- **Criptografia Assimétrica:** Na criptografia assimétrica, são usadas duas chaves diferentes: uma chave pública e uma chave privada. A chave pública é usada para criptografar os dados, enquanto a chave privada é usada para descriptografar os dados. Isso permite que qualquer pessoa envie mensagens criptografadas para o destinatário, que pode descriptografá-las usando sua chave privada. Exemplos de algoritmos de criptografia assimétrica incluem o RSA (Rivest-Shamir-Adleman) e o ECC (Elliptic Curve Cryptography).

Mas nosso foco será falar sobre hashes, se quiser saber mais sobre criptografia simétrica e assimétrica, recomendo a leitura do [artigo da Wikipedia](https://pt.wikipedia.org/wiki/Criptografia).

## Hashes

Um hash é uma função matemática que transforma um conjunto de dados em uma sequência de caracteres alfanuméricos de comprimento fixo. O hash é usado para verificar a integridade dos dados, garantindo que eles não foram alterados ou corrompidos. Um hash é uma representação única dos dados originais e é usado em várias aplicações, como verificação de arquivos, senhas seguras e autenticação de mensagens.

Um exemplo:

- **Texto Original:** `Insper`
- **Hash MD5:** `ee40f5397d757da12fac91b6d30c12db`

### Um pouco da história

O conceito de hash foi introduzido por Hans Peter Luhn em 1953, que propôs o uso de funções hash para indexar e pesquisar registros em bancos de dados. Desde então, os hashes têm sido amplamente utilizados em várias aplicações de segurança e criptografia.

Como antigamente as senhas eram guardadas em texto puro, era fácil para um atacante obter as senhas dos usuários. Para resolver esse problema, as senhas passaram a ser armazenadas como hashes, tornando mais difícil para um atacante obter as senhas originais.

Como um hash funciona?

Bom, um hash é uma função matemática que transforma um conjunto de dados em uma sequência de caracteres alfanuméricos de comprimento fixo. O hash é calculado com base nos dados originais e é único para cada conjunto de dados. Isso significa que, mesmo que os dados originais sejam ligeiramente diferentes, o hash resultante será completamente diferente.

Por exemplo, o Insper e o Insper1 terão hashes completamente diferentes.

| Texto Original | Hash MD5                           |
|-----------------|-----------------------------------|
| Insper          | ee40f5397d757da12fac91b6d30c12db  |
| Insper1         | 64dc56a51bec0321b443de1c15c1c812  |

Mas com o passar dos anos o hash md5 virou um problema, por ter uma quantidade de caracteres pequena muitas pessoas conseguiram criar tabelas de hash e descobrir o texto original, alem disso, pela pequena quantidade de caracteres, muitas vezes se repetiam hashs para diferentes textos.

## Novos Hashes

Para resolver esses problemas, foram desenvolvidos novos algoritmos de hash, como o SHA1 (Secure Hash Algorithm 1), SHA256 e SHA512. Esses algoritmos produzem hashes mais longos e mais seguros, tornando mais difícil para um atacante descobrir o texto original a partir do hash.

Um exemplo:

- **Texto Original:** `Insper`
- **Hash SHA256:** `8dce2900a8e96750219195b313b96246992c347b588f6181f1206860ac50c42f`

Pode-se notar que o hash SHA256 é muito mais longo e mais seguro do que o hash MD5, tornando mais difícil para um atacante descobrir o texto original.

Hoje em dia o SHA256 é muito utilizado para armazenar senhas, mas ainda assim, atacantes ja estão conseguindo quebrar esses hashes, por isso, hoje em dia é usado o SHA512 + Random Salt, que adiciona um valor aleatório ao texto antes de gerar o hash, tornando ainda mais difícil para um atacante descobrir o texto original.

Antes de falar sobre como funciona o Random Salt, vamos falar o porque que ele foi adotado.

## Rainbow Tables

Como um hash é um tipo de criptografia de mão única, não é possível descriptografar um hash para obter o texto original. No entanto, os atacantes podem usar tabelas de arco-íris (rainbow tables) para descobrir o texto original a partir de um hash.

Uma tabela de arco-íris é uma tabela de hash pré-computada que mapeia hashes para textos originais. Os atacantes podem usar essas tabelas para procurar um hash específico e encontrar o texto original correspondente. Isso torna mais fácil para um atacante descobrir senhas e outras informações confidenciais.

> **Palavras do Maraba**: "Alguem um dia virou e falou. 'E se eu fizer uma tabela com todos os hashs possiveis?' e assim nasceu as rainbow tables"

Para contornar esse problema, foi introduzido o conceito de Random Salt.

## Random Salt

O Random Salt é um valor aleatório que é adicionado ao texto antes de gerar o hash. Esse valor aleatório torna o hash único, mesmo que o texto original seja o mesmo. Isso significa que, mesmo que dois usuários tenham a mesma senha, seus hashes serão diferentes devido ao Random Salt.

Com o Random Salt, os atacantes não podem usar tabelas de arco-íris para descobrir senhas, pois cada hash é único e não pode ser pré-computado. Isso torna impossível para um atacante descobrir senhas a partir de hashes (Por enquanto).