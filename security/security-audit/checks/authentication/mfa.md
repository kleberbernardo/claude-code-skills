# MFA — Autenticação Multi-Fator

Auditoria de implementação e enforcement de MFA.

---

## Verificações Principais

### Disponibilidade de MFA
```bash
grep -rn "totp\|hotp\|speakeasy\|authenticator\|2fa\|mfa\|two.factor\|otp" --include="*.{js,ts,py,go}" -i -l
```

### Enforcement para Contas Privilegiadas
**Severidade: High se ausente para admins**

```bash
# Verificar se admin requer MFA
grep -rn "isAdmin\|role.*admin\|admin.*role" --include="*.{js,ts,py}" | grep -i "mfa\|2fa\|totp"
```

---

## Vulnerabilidades Comuns

### 1. Bypass de MFA em Reset de Senha
Após reset de senha, MFA deve continuar requerido.

### 2. Força Bruta em Código OTP
**TOTP tem apenas 10^6 possibilidades (6 dígitos)**

```bash
# Verificar rate limit no endpoint de MFA
grep -rn "verifyOtp\|verify.*otp\|check.*code\|mfa.*verify" --include="*.{js,ts,py}" | grep -v "rateLimit\|throttle"
```

### 3. Replay de OTP
Código OTP deve ser invalidado após uso.

### 4. Backup Codes Fracos
```bash
# Verificar geração de backup codes
grep -rn "backupCode\|backup_code\|recovery_code" --include="*.{js,ts,py}"
# Devem ter entropia suficiente e ser hashed no banco
```

---

## Checklist

- [ ] MFA disponível para todos os usuários
- [ ] MFA obrigatório para admins e usuários privilegiados
- [ ] Rate limiting no endpoint de verificação OTP
- [ ] Código OTP de uso único (anti-replay)
- [ ] Backup codes com entropia adequada e armazenados com hash
- [ ] MFA não bypassável via reset de senha
- [ ] Janela de tempo TOTP razoável (± 30 segundos)

---

## Red Flags

1. Sem MFA disponível em sistema com dados sensíveis
2. MFA não requerido para contas de admin
3. Sem rate limiting no endpoint de verificação MFA
4. OTP pode ser reutilizado (sem invalidação após uso)
