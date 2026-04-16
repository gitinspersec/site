# Fundamentos Matemáticos da Criptografia

Objetivo: Entender as bases matemáticas por trás dos algoritmos criptográficos modernos, começando por cifras clássicas e evoluindo para RSA e curvas elípticas.

---

## 1. Cifras Clássicas: Introdução à Criptografia

Antes da era digital, cifras eram baseadas em substituição e transposição. Elas ilustram conceitos fundamentais como chave e reversibilidade.

### 1.1. Cifra de César

A cifra de César é uma substituição simples: cada letra é deslocada por um número fixo (chave) no alfabeto.

- **Exemplo**: Chave = 3. A → D, B → E, ..., Z → C.
- **Encriptação**: `C = (P + K) mod 26`, onde P é a posição da letra (A=0).
- **Decriptação**: `P = (C - K) mod 26`.
- **Fraqueza**: Apenas 25 possibilidades, fácil de brute-force.

**Exemplo em Python**:
```python
def cesar(texto, chave, modo='encriptar'):
    resultado = ""
    for char in texto.upper():
        if char.isalpha():
            base = ord('A')
            if modo == 'encriptar':
                novo = (ord(char) - base + chave) % 26 + base
            else:
                novo = (ord(char) - base - chave) % 26 + base
            resultado += chr(novo)
        else:
            resultado += char
    return resultado

print(cesar("HELLO", 3))  # KHOOR
print(cesar("KHOOR", 3, 'decriptar'))  # HELLO
```

### 1.2. Cifra Afim

Uma generalização da César: usa multiplicação e adição. `C = (a * P + b) mod 26`.

- **Condição**: `a` deve ser coprimo com 26 (gcd(a, 26) = 1) para ser reversível.
- **Decriptação**: Encontrar inverso modular de `a` (a_inv), então `P = a_inv * (C - b) mod 26`.
- **Fraqueza**: Mais segura que César, mas ainda vulnerável a análise de frequência.

**Exemplo**: a=5, b=8. "A" (0) → (5*0 + 8) mod 26 = 8 → "I".

---

## 2. Aritmética Modular

Fundamental para algoritmos modernos. Operações em um "anel" finito.

### 2.1. Conceitos Básicos

- **Módulo**: `a ≡ b mod n` significa `n` divide `(a - b)`.
- **Operações**: Adição, subtração, multiplicação mod n.
- **Exponenciação**: `a^b mod n` (usada em RSA).

### 2.2. Inverso Modular

Número `x` tal que `(a * x) ≡ 1 mod n`. Existe se gcd(a, n) = 1.

- **Exemplo**: Inverso de 3 mod 26 é 9, pois 3*9=27≡1 mod 26.

### 2.3. Teorema de Euler

Se gcd(a, n) = 1, então `a^φ(n) ≡ 1 mod n`, onde φ(n) é a função totiente.

- Para n = p*q (primos), φ(n) = (p-1)*(q-1).

---

## 3. RSA: Criptografia Assimétrica

RSA (Rivest-Shamir-Adleman) usa aritmética modular para encriptação e assinaturas.

### 3.1. Geração de Chaves

1. Escolher dois primos grandes p e q.
2. Calcular n = p * q (módulo público).
3. Calcular φ(n) = (p-1)*(q-1).
4. Escolher e (1 < e < φ(n)) coprimo com φ(n) — chave pública (n, e).
5. Calcular d = inverso de e mod φ(n) — chave privada (d).

### 3.2. Encriptação

`C = M^e mod n`, onde M é a mensagem (como número).

### 3.3. Decriptação

`M = C^d mod n`.

**Por que funciona?** Pelo teorema de Euler: `M^{e*d} ≡ M mod n`, pois e*d ≡ 1 mod φ(n), então M^{k*φ(n) + 1} ≡ M mod n.

### 3.4. Assinaturas

- Assinar: `S = M^d mod n` (com chave privada).
- Verificar: `M ≡ S^e mod n` (com chave pública).

### 3.5. Segurança

Baseada na dificuldade de fatorar n em p e q. Para n de 2048 bits, é computacionalmente inviável.

**Exemplo Simples** (pequeno para ilustração):
- p=5, q=11, n=55, φ=40.
- e=3 (coprimo com 40).
- d=27 (3*27=81≡1 mod 40).
- Mensagem M=7: C=7^3 mod 55=343 mod 55=343-6*55=343-330=13.
- Decriptar: 13^27 mod 55=7.

---

## 4. Curvas Elípticas

Usadas em ECC (Elliptic Curve Cryptography), como ed25519, para eficiência.

### 4.1. Definição

Uma curva elíptica é `y² = x³ + a*x + b mod p`, com discriminante ≠0.

- **Exemplo**: secp256k1 (Bitcoin): y² = x³ + 7 mod p, p primo grande.

### 4.2. Grupo e Adição

Pontos formam grupo abeliano com operação de adição.

- **Adição de P + Q**: Linha reta por P e Q intersece curva em -R, refletir para R.
- **Duplicação**: Tangente em P intersece em -R, refletir.
- **Identidade**: Ponto no infinito O.

### 4.3. Logaritmo Discreto

Dado P e Q = k*P, encontrar k é difícil (base da segurança).

### 4.4. ECC vs RSA

- ECC: Chave de 256 bits ~ segurança de RSA 3072 bits.
- Mais eficiente em CPU e largura de banda.

### 4.5. ed25519

- Curva: Twisted Edwards.
- Assinaturas determinísticas, sem colisões conhecidas.
- Usado em SSH, Git, blockchains.

---

## 5. Números Primos e Fatoração

- **Geração**: Testes probabilísticos (Miller-Rabin).
- **Fatoração**: Difícil para números grandes (base de RSA).

---

## Resumo

Da simplicidade das cifras clássicas à complexidade de RSA e ECC, a matemática torna a criptografia segura. Pratique com pequenos exemplos para entender os conceitos.</content>
<parameter name="filePath">d:\Insper\InsperSec\site\inspersec\docs\aulas\blueTeam\aula4\criptografia_fundamentos_matematicos.md