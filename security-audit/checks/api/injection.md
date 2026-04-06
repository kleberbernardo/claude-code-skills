# Injeção — SQL, NoSQL, Command, LDAP, Template

A família de vulnerabilidades mais crítica em aplicações web. #1 no OWASP Top 10 historicamente.

---

## SQL Injection

### Padrões de Alta Confiança

```bash
# Concatenação direta em SQL
grep -rn "\"SELECT.*\+\|\"UPDATE.*\+\|\"INSERT.*\+\|\"DELETE.*\+" --include="*.{js,ts,py,go,php,java}" -n
grep -rn "f\"SELECT\|f\"UPDATE\|f\"INSERT\|f\"DELETE" --include="*.py" -n
grep -rn "\`SELECT.*\${" --include="*.{js,ts}" -n

# Format strings
grep -rn "\"SELECT.*%s\|\"SELECT.*%d\|\"SELECT.*%\(" --include="*.py" | grep -v "parameterized\|prepare"

# Raw queries com template literals
grep -rn "\.raw\s*(\`\|\.query\s*(\`\|\.execute\s*(\`" --include="*.{js,ts}" -n

# Knex raw
grep -rn "knex\.raw\|db\.raw\|sequelize\.query" --include="*.{js,ts}" -n
```

### Exemplos Vulneráveis

```javascript
// CRÍTICO — concatenação direta
const result = await db.query(`SELECT * FROM users WHERE email = '${req.body.email}'`)

// CRÍTICO — template literal
const result = await knex.raw(`SELECT * FROM orders WHERE id = ${req.params.id}`)

// CRÍTICO — formatação de string Python
cursor.execute("SELECT * FROM users WHERE id = %s" % user_id)
# (Note: %s com % é interpolação — inseguro; com tupla (user_id,) é seguro)
```

### Exemplos Seguros

```javascript
// Queries parametrizadas — SEGURO
const result = await db.query('SELECT * FROM users WHERE email = $1', [req.body.email])

// ORM com métodos de query — SEGURO
const user = await User.findOne({ where: { email: req.body.email } })

// Knex com bindings — SEGURO
const result = await knex.raw('SELECT * FROM orders WHERE id = ?', [req.params.id])
```

---

## NoSQL Injection (MongoDB)

### Padrões de Alta Confiança

```bash
grep -rn "find\s*(\s*{.*req\.\|findOne\s*(\s*{.*req\." --include="*.{js,ts}" -n
grep -rn "\$where\|\$regex.*req\.\|\$gt.*req\." --include="*.{js,ts,py}" -n
```

### Exemplos Vulneráveis

```javascript
// CRÍTICO — input direto em query MongoDB
const user = await User.findOne({ username: req.body.username })
// Payload: { "username": { "$gt": "" } }  → retorna todos os usuários
// Payload: { "username": { "$regex": ".*" } } → todos os usuários

// CRÍTICO — $where com input do usuário
db.users.find({ $where: `this.username === '${username}'` })
```

### Proteção

```javascript
// Sanitizar input MongoDB
import { sanitize } from 'mongo-sanitize'

const safeInput = sanitize(req.body)
const user = await User.findOne({ username: safeInput.username })

// Ou usar mongoose com tipos definidos
const user = await User.findOne({ username: String(req.body.username) })
```

---

## Command Injection

### Padrões de Alta Confiança (Critical)

```bash
grep -rn "exec\s*(\|system\s*(\|popen\s*(\|spawn\s*(" --include="*.{py,rb,php}" -n
grep -rn "child_process\.exec\|execSync\|spawnSync" --include="*.{js,ts}" -n
grep -rn "subprocess\.call\|subprocess\.run\|os\.system\|os\.popen" --include="*.py" -n
grep -rn "shell=True" --include="*.py" -n  # Python subprocess com shell=True
```

### Exemplos Vulneráveis

```javascript
// CRÍTICO — exec com input do usuário
const { exec } = require('child_process')
exec(`convert ${req.body.filename} output.pdf`, callback)
// Payload: "file.png && rm -rf /"

// CRÍTICO — Python com shell=True
import subprocess
subprocess.run(f"ping {host}", shell=True)
# Payload: "google.com; cat /etc/passwd"
```

### Proteção

```javascript
// Usar array de argumentos, sem shell
const { spawn } = require('child_process')
spawn('convert', [filename, 'output.pdf'])  // sem shell, argumentos separados

// Python sem shell=True
subprocess.run(['ping', '-c', '1', host], shell=False)
```

---

## Template Injection (SSTI)

```bash
grep -rn "render_template_string\|Environment.*from_string\|\.render\s*(\s*template" --include="*.py" -n
grep -rn "nunjucks\.renderString\|ejs\.render\s*(\s*template" --include="*.{js,ts}" -n
grep -rn "Blade::render\|view\.make\s*(\s*\$" --include="*.php" -n
```

### Exemplo Vulnerável (Python Jinja2)

```python
# CRÍTICO — render com input do usuário
from jinja2 import Environment
env = Environment()
template = env.from_string(f"Hello {request.args.get('name')}")
# Payload: {{ config.SECRET_KEY }} ou {{ ''.__class__.__mro__[2].__subclasses__() }}
```

---

## LDAP Injection

```bash
grep -rn "ldap\|ldapsearch\|DirectorySearcher\|LdapConnection" --include="*.{js,ts,py,java,cs}" -n
```

---

## Checklist

- [ ] Toda query SQL usa parâmetros (?, $1, :param)
- [ ] Nenhuma concatenação de string em SQL
- [ ] Input MongoDB sanitizado antes de usar em queries
- [ ] `shell=True` (Python) ou `exec()` (Node) não usam input do usuário
- [ ] Templates não renderizam input do usuário diretamente
- [ ] ORM/Query builder usado corretamente (sem `.raw()` com input)

---

## Red Flags Críticas

1. Template literals em queries SQL: `` `SELECT * FROM users WHERE id = ${id}` ``
2. `shell=True` com variável de input do usuário
3. `eval()` ou `exec()` com input externo
4. MongoDB `$where` com input do usuário
5. Jinja2/EJS renderizando input do usuário como template
