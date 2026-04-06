# Secrets Expostos no Frontend

Chaves e tokens que vazam para o cliente via bundle JavaScript, HTML ou source maps.

---

## O Problema

Qualquer variável acessível no browser pode ser lida por qualquer pessoa. Isso inclui:
- Chaves de API "públicas" de serviços que deveriam ser privadas
- Tokens de autenticação hardcoded
- Endpoints internos e estrutura da API
- Dados de configuração sensíveis

---

## Busca no Código

```bash
# Next.js — variáveis NEXT_PUBLIC_ são expostas ao cliente (intencional mas verificar o que está exposto)
grep -rn "NEXT_PUBLIC_" --include="*.{js,ts,jsx,tsx,env*}" -n

# Variáveis de ambiente no código client-side
grep -rn "process\.env\." --include="*.{js,ts,jsx,tsx}" | grep -v "NODE_ENV\|NEXT_PUBLIC_\|REACT_APP_"

# Secrets hardcoded em código client-side
grep -rn "apiKey\s*:\|api_key\s*:\|secret\s*:\|token\s*:" --include="*.{js,ts,jsx,tsx}" | grep -v "//\|process\.env"

# Firebase config (pode ter apiKey que parece sensível mas é pública por design)
grep -rn "firebaseConfig\|initializeApp" --include="*.{js,ts,jsx,tsx}" -n

# Google Maps, Stripe publishable keys
grep -rn "pk_live_\|pk_test_\|AIza" --include="*.{js,ts,jsx,tsx}" -n

# Source maps em produção
find . -name "*.js.map" -path "*/build/*" -o -name "*.js.map" -path "*/dist/*"
```

---

## O que É vs. Não É Problema

### NÃO é problema (chaves públicas por design):
- `NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY` (pk_live_ / pk_test_) — é pública
- `NEXT_PUBLIC_GOOGLE_MAPS_API_KEY` — pública mas com restrições de domínio
- Firebase `apiKey` — pública, segurança via Security Rules
- `NEXT_PUBLIC_SUPABASE_URL` e `NEXT_PUBLIC_SUPABASE_ANON_KEY` — públicas por design

### É problema:
- `sk_live_` (Stripe SECRET key) — CRÍTICO
- `OPENAI_API_KEY` exposta no bundle — CRÍTICO
- Senha de banco de dados no frontend
- JWT secret no código client-side
- Chave privada RSA/AWS no frontend

---

## Source Maps em Produção

Source maps expõem código-fonte original ao público.

```bash
# Verificar se source maps são gerados em produção
cat next.config.js | grep -i "sourceMap\|productionBrowserSourceMaps"
cat webpack.config.js | grep -i "devtool\|sourceMap"
```

**Next.js:** por padrão não gera source maps em produção (seguro). Mas se `productionBrowserSourceMaps: true` está configurado, reconsiderar.

---

## Checklist

- [ ] Nenhum secret key (sk_live_, sk_test_, OPENAI_API_KEY) exposto no bundle
- [ ] Variáveis de servidor não expostas no cliente
- [ ] Source maps não expostos em produção
- [ ] Chaves públicas têm restrições adequadas (ex: Google Maps com domain restriction)
- [ ] `.env.local` não commitado com valores sensíveis

---

## Red Flags

1. `sk_live_` ou `sk_test_` em arquivo JavaScript do cliente
2. `OPENAI_API_KEY`, `ANTHROPIC_API_KEY` em variáveis NEXT_PUBLIC_
3. Source maps públicos em produção revelando código proprietário
4. Senha de banco em bundle JavaScript (pode acontecer com Prisma Edge)
