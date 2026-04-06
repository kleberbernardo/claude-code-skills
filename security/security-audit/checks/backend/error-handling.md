# Error Handling — Vazamento de Informação

Respostas de erro inadequadas expõem stack traces, estrutura interna e versões de componentes.

---

## Vazamentos Críticos

### Stack Traces em Produção

```bash
# Verificar se stack traces são enviados ao cliente
grep -rn "res\.json.*error.*stack\|res\.send.*err\.stack\|response.*traceback" --include="*.{js,ts,py}" -n
grep -rn "DEBUG\s*=\s*True\|app\.debug\s*=\s*True" --include="*.py" -n

# Express — error handler padrão manda stack em dev
grep -rn "app\.use\s*(\s*(err\b" --include="*.{js,ts}" -n  # error handler
```

**Exemplo vulnerável:**
```javascript
// VULNERÁVEL — expõe stack trace
app.use((err, req, res, next) => {
  res.status(500).json({
    error: err.message,
    stack: err.stack,       // NUNCA em produção
    details: err.toString() // NUNCA em produção
  })
})

// SEGURO
app.use((err, req, res, next) => {
  const isDev = process.env.NODE_ENV === 'development'
  
  // Log completo no servidor
  logger.error({ err, url: req.url, userId: req.user?.id })
  
  // Resposta genérica ao cliente
  res.status(err.status || 500).json({
    error: isDev ? err.message : 'Internal server error',
  })
})
```

---

### Mensagens de Erro Informativas Demais

```bash
# Mensagens que revelam estrutura interna
grep -rn "\"Cannot find column\|\"Table.*doesn't exist\|\"column.*does not exist\|\"relation.*does not exist" --include="*.{js,ts,py}" | grep "res\.\|return\|raise\|throw"
```

**Exemplos a evitar:**
```
// Expõe estrutura do banco
"SQLSTATE[42703]: column 'user_email' does not exist in table 'users'"

// Expõe tecnologia usada
"NullPointerException at com.myapp.UserService.findUser(UserService.java:47)"

// Expõe configuração
"Connection refused: postgresql://admin:***@internal-db:5432/prod"
```

---

### Debug Endpoints em Produção

```bash
grep -rn "debug\|/debug\|__debug\|.debug\." --include="*.{js,ts,py}" | grep "route\|endpoint\|path\|url" -i
grep -rn "\"/health\|\"/status\|\"/info\|\"/metrics\|\"/actuator" --include="*.{js,ts,py}" -n
```

**Verificar:**
- `/actuator` (Spring Boot) — pode expor configuração, heap dumps, env vars
- `/debug/vars` (Go) — expõe variáveis internas
- Flask debug toolbar acessível em produção
- Django DEBUG = True em produção

---

## Checklist

- [ ] Stack traces não enviados ao cliente em produção
- [ ] Mensagens de erro genéricas para o cliente, detalhes apenas em logs do servidor
- [ ] Debug mode desativado em produção
- [ ] Endpoints de debug/metrics protegidos ou removidos
- [ ] Versões de software não expostas em headers (X-Powered-By, Server)
- [ ] Erros de banco não vazados diretamente ao cliente

---

## Security Headers

```bash
# Verificar remoção de headers que expõem tecnologia
grep -rn "x-powered-by\|X-Powered-By" --include="*.{js,ts}" -i
# Express por padrão adiciona X-Powered-By: Express
# helmet() remove isso automaticamente
grep -rn "helmet\(\)" --include="*.{js,ts}" -l
```

---

## Red Flags

1. `res.json({ stack: err.stack })` em error handler de produção
2. `DEBUG = True` em settings de produção (Django/Flask)
3. `/actuator/env` acessível sem autenticação
4. Erro de SQL retornado diretamente ao usuário
