# Segurança em Banco de Dados — SQL e NoSQL

Verificação de configurações e padrões de acesso ao banco.

---

## SQL — Injeção e Queries Seguras

Ver `checks/api/injection.md` para padrões de SQL injection.

### Queries Dinâmicas Perigosas

```bash
# ORDER BY dinâmico (não parametrizável — risco de injection)
grep -rn "ORDER BY.*req\.\|ORDER BY.*\${" --include="*.{js,ts,py}" -n

# Table ou column name dinâmico
grep -rn "FROM.*\${table\|SELECT.*\${column\|INTO.*\${table" --include="*.{js,ts,py}" -n
```

**ORDER BY dinâmico seguro:**
```javascript
// VULNERÁVEL — injeção via ORDER BY
const query = `SELECT * FROM products ORDER BY ${req.query.sort}`

// SEGURO — whitelist de colunas permitidas
const ALLOWED_SORT_COLUMNS = ['name', 'price', 'created_at']
const sortColumn = ALLOWED_SORT_COLUMNS.includes(req.query.sort)
  ? req.query.sort
  : 'created_at'
const query = `SELECT * FROM products ORDER BY ${sortColumn}`
```

---

## NoSQL — MongoDB

```bash
# Operadores MongoDB em input do usuário
grep -rn "\$where\|\$gt.*req\.\|\$regex.*req\.\|\$nin.*req\." --include="*.{js,ts}" -n

# Projeção com input do usuário
grep -rn "\.select\s*(\s*req\.\|projection.*req\." --include="*.{js,ts}" -n
```

---

## Configuração de Conexão

```bash
# Connection string no código
grep -rn "mongoose\.connect\|createConnection\|new Sequelize\|createPool" --include="*.{js,ts,py}" -n | grep -v "process\.env"

# SSL/TLS na conexão
grep -rn "ssl.*false\|sslmode.*disable\|rejectUnauthorized.*false" --include="*.{js,ts,py,env}" -n
```

**Verificar:**
```javascript
// Conexão deve usar TLS em produção
const pool = new Pool({
  connectionString: process.env.DATABASE_URL,
  ssl: process.env.NODE_ENV === 'production' 
    ? { rejectUnauthorized: true }  // verificar certificado
    : false
})
```

---

## Queries sem LIMIT

```bash
# Queries que podem retornar datasets enormes
grep -rn "\.findAll\(\|\.find\(\|\.findMany\s*(\s*{}" --include="*.{js,ts,py}" | grep -v "limit\|take\|first\|LIMIT"

# SQL sem LIMIT
grep -rn "SELECT \*.*FROM" --include="*.{js,ts,py}" | grep -v "LIMIT\|limit\|TOP\|ROWNUM"
```

---

## Checklist

- [ ] Nenhuma concatenação de string em queries SQL
- [ ] ORDER BY dinâmico usa whitelist de colunas
- [ ] Connection string vem de variável de ambiente
- [ ] Conexão com banco usa TLS em produção
- [ ] Queries de listagem têm LIMIT e paginação
- [ ] Sem queries N+1 em loops (performance e DoS)
- [ ] Timeout configurado para queries longas

---

## Red Flags

1. `ORDER BY ${req.query.sort}` sem whitelist
2. `FROM ${tableName}` com input do usuário
3. `ssl: { rejectUnauthorized: false }` em produção (MITM possível)
4. `findAll()` ou `find({})` sem limite em endpoint público
