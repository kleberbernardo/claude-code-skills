# Hashing de Senhas

Verificação de algoritmos de hashing de senhas. Um dos erros mais comuns e mais caros em segurança.

---

## Algoritmos — Classificação

### Inseguros (CRITICAL se encontrado em produção)
| Algoritmo | Problema | Tempo de brute force |
|-----------|----------|---------------------|
| MD5 | Colisões, sem salt, muito rápido | Bilhões/segundo em GPU |
| SHA1 | Colisões, sem salt, rápido | Bilhões/segundo em GPU |
| SHA256 | Sem salt, muito rápido | Centenas de milhões/seg |
| SHA512 | Sem salt, muito rápido | Dezenas de milhões/seg |
| DES/crypt | Obsoleto, salt apenas 2 chars | Trivial |
| Plaintext | Sem hashing | Instantâneo |

### Seguros (mínimo aceitável)
| Algoritmo | Recomendação |
|-----------|-------------|
| bcrypt | Recomendado, fator de trabalho 12+ |
| Argon2id | Melhor opção atual (vencedor PHC) |
| scrypt | Seguro, mais complexo de configurar |
| PBKDF2-SHA256 | Aceitável, 600k+ iterações (NIST) |

---

## Busca por Algoritmos Inseguros

```bash
# Python
grep -rn "hashlib\.md5\|hashlib\.sha1\|hashlib\.sha256\|hashlib\.sha512" --include="*.py"
grep -rn "import md5\|from hashlib import" --include="*.py" | grep "md5\|sha1"
grep -rn "crypt\.crypt\|crypt\.md5" --include="*.py"

# JavaScript/Node.js
grep -rn "crypto\.createHash\s*(\s*['\"]md5\|crypto\.createHash\s*(\s*['\"]sha1\|crypto\.createHash\s*(\s*['\"]sha256" --include="*.{js,ts}"
grep -rn "md5(\|sha1(\|sha256(" --include="*.{js,ts}" | grep -i "password\|senha\|pwd\|pass"

# Go
grep -rn "md5\.\|sha1\.\|sha256\." --include="*.go" | grep -i "password\|hash\|digest"

# PHP
grep -rn "md5(\s*\$password\|sha1(\s*\$password\|sha256(\s*\$password\|hash\s*(\s*['\"]md5\|hash\s*(\s*['\"]sha1" --include="*.php"

# Java
grep -rn "MessageDigest\.getInstance\s*(\s*[\"']MD5\|MessageDigest\.getInstance\s*(\s*[\"']SHA-1\|MessageDigest\.getInstance\s*(\s*[\"']SHA-256" --include="*.java" 
```

---

## Busca por Implementações Corretas

```bash
# Python — verificar uso de bcrypt/argon2
grep -rn "bcrypt\|argon2\|scrypt\|passlib" --include="*.py" -l

# Node.js — verificar uso de bcrypt/argon2
grep -rn "bcrypt\|argon2\|@node-rs/argon2\|@node-rs/bcrypt" --include="*.{json,js,ts}"

# PHP — verificar password_hash
grep -rn "password_hash\|PASSWORD_BCRYPT\|PASSWORD_ARGON2" --include="*.php"

# Java — verificar BCryptPasswordEncoder
grep -rn "BCryptPasswordEncoder\|Argon2PasswordEncoder" --include="*.java"
```

---

## Verificação de Configuração

```python
# Python — verificar fator de trabalho do bcrypt
import bcrypt
# Fator 10 é mínimo, 12 é recomendado
bcrypt.hashpw(password.encode(), bcrypt.gensalt(rounds=12))

# Inseguro — rounds muito baixos
bcrypt.gensalt(rounds=4)   # FRACO
bcrypt.gensalt(rounds=8)   # Abaixo do recomendado
```

```javascript
// Node.js — verificar saltRounds
const saltRounds = 10  // mínimo
const saltRounds = 12  // recomendado
// const saltRounds = 4  // FRACO — alerta
await bcrypt.hash(password, saltRounds)
```

---

## Checklist

- [ ] Senhas hashadas com bcrypt, argon2id ou scrypt
- [ ] Nenhum MD5 ou SHA1 para senhas
- [ ] Nenhuma senha em plaintext no banco
- [ ] Fator de trabalho adequado (bcrypt rounds ≥ 10, argon2 com parâmetros adequados)
- [ ] Salt único por senha (bcrypt e argon2 fazem isso automaticamente)
- [ ] Comparação timing-safe (`bcrypt.compare`, não `==`)
- [ ] Nenhuma senha em log ou resposta de API

---

## Red Flags

1. `hashlib.md5(password)` — CRITICAL
2. `hashlib.sha256(password.encode()).hexdigest()` sem pbkdf2 — HIGH
3. `crypto.createHash('md5').update(password)` — CRITICAL
4. `password_hash($password, PASSWORD_DEFAULT)` com PHP default antigo — verificar
5. Senha armazenada em texto plano (checar schema do banco)
6. `bcrypt.gensalt(rounds=4)` — rounds muito baixos

---

## Verificação no Schema do Banco

```sql
-- Checar tipo e tamanho da coluna password
-- bcrypt: sempre 60 chars
-- argon2: ~97 chars
-- MD5: 32 chars (ALERTA)
-- SHA1: 40 chars (ALERTA)
-- SHA256: 64 chars (SUSPEITO)
-- plaintext: variável (CRÍTICO)

-- Se encontrar colunas com tamanho fixo de 32 ou 40, investigar
```

---

## Caso Real de Falha

**Adobe 2013:** 153 milhões de senhas vazadas hashadas com DES + mesmo salt para todas, em modo de cifragem (não hashing). Permitiu deduzir senhas por padrão — senhas iguais tinham hashes iguais.

**Rockyou 2009:** 32 milhões de senhas em texto plano. Possibilitou criar a wordlist mais usada em brute force até hoje.
