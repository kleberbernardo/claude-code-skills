# Política de Senhas e Proteção

Auditoria de política de senhas, brute force protection e fluxo de reset.

---

## Política de Senhas

### Mínimo Aceitável
- Comprimento mínimo: **12 caracteres** (NIST 2024)
- Sem restrições arbitrárias de complexidade obrigatória
- Verificar contra senhas conhecidas comprometidas (HaveIBeenPwned)
- Permitir espaços e caracteres especiais

### Verificações
```bash
# Buscar validação de senha
grep -rn "minLength\|min_length\|password.*length\|len(password)\|password\.length" --include="*.{js,ts,py,go,rb}"

# Verificar se há integração com pwned passwords
grep -rn "haveibeenpwned\|pwnedpasswords\|breached" --include="*.{js,ts,py}" -i
```

**Red flags:**
```javascript
// Comprimento mínimo muito baixo
if (password.length < 6) throw new Error(...)  // INSEGURO — mínimo é 8, ideal 12
if (password.length > 64) throw new Error(...)  // Truncating — pode ser bcrypt bypass
```

---

## Brute Force Protection

### Rate Limiting em Login
**Severidade: High se ausente**

```bash
# Verificar se há rate limiting no endpoint de login
grep -rn "rateLimit\|rate_limit\|express-rate-limit\|slowDown\|throttle" --include="*.{js,ts,py}" -l

# Verificar se está aplicado no endpoint de login especificamente
grep -B 5 -A 5 "login\|signin\|auth" $(grep -rn "rateLimit\|rate_limit" --include="*.{js,ts}" -l)
```

### Account Lockout
```bash
# Verificar tentativas máximas de login
grep -rn "loginAttempts\|login_attempts\|failed_attempts\|lockout\|lock_account" --include="*.{js,ts,py,go}" -n
```

### CAPTCHA
```bash
grep -rn "recaptcha\|hcaptcha\|turnstile\|captcha" --include="*.{js,ts,py}" -i -l
```

---

## Password Reset

**Fluxo seguro obrigatório:**

1. Usuário solicita reset com email
2. Gerar token único de alta entropia (mínimo 32 bytes)
3. Token com expiração curta (15-60 minutos)
4. Token de uso único (invalidar após usar)
5. Email não confirmar se usuário existe (anti-enumeração)
6. Nova senha obrigatória (não reutilizar token para login)

```bash
# Verificar geração de token de reset
grep -rn "resetToken\|reset_token\|passwordReset\|forgot.*token" --include="*.{js,ts,py}" -n

# Verificar se usa crypto seguro
grep -rn "crypto\.randomBytes\|secrets\.token\|SecureRandom\|uuid" --include="*.{js,ts,py}" | grep -i "reset\|token"

# Red flag: Math.random em reset token
grep -rn "Math\.random\(\)" --include="*.{js,ts}" | grep -i "token\|reset\|secret"
```

**Verificar:**
- [ ] Token gerado com CSPRNG (não Math.random ou UUID v1/v4 com seed previsível)
- [ ] Token expira em 15-60 minutos
- [ ] Token é invalidado após uso
- [ ] Token é invalidado se novo reset for solicitado
- [ ] Resposta não varia entre email existente e não-existente (anti-enumeração)
- [ ] Link de reset via HTTPS sempre

---

## Enumeração de Usuários

Um endpoint de login que responde diferente para "usuário não existe" vs "senha errada" permite enumerar usuários válidos.

```bash
# Verificar resposta diferenciada
grep -rn "User not found\|user.*not.*exist\|email.*not.*registered\|Invalid.*email" --include="*.{js,ts,py}" -i
```

**Seguro:** Sempre retornar "Credenciais inválidas" — nunca especificar qual campo está errado.

---

## Checklist

- [ ] Comprimento mínimo de 12 caracteres
- [ ] Rate limiting no endpoint de login
- [ ] Account lockout após N tentativas falhas
- [ ] Token de reset com alta entropia e expiração
- [ ] Token de reset de uso único
- [ ] Resposta anti-enumeração (mesma resposta para user inexistente e senha errada)
- [ ] Reset link enviado apenas por HTTPS
- [ ] MFA disponível (ver checks/authentication/mfa.md)

---

## Red Flags

1. Sem rate limiting em `/login` ou `/api/auth`
2. `Math.random()` gerando token de reset
3. Token de reset sem expiração
4. Resposta diferente: "Email não encontrado" vs "Senha incorreta"
5. Senha mínima < 8 caracteres
6. Senha máxima ≤ 20 caracteres (pode indicar armazenamento sem hash)
