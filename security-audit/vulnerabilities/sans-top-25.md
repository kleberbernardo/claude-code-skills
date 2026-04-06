# SANS/CWE Top 25 Most Dangerous Software Weaknesses

As 25 fraquezas de software mais perigosas segundo MITRE CWE / SANS.

---

## Top 10 Mais Críticos

### CWE-787: Out-of-bounds Write
**Score: 65.93** | Relevante em C/C++, mas também em buffers em Node.js/Python bindings.

### CWE-79: Cross-site Scripting (XSS)
**Score: 45.54** | Ver `checks/frontend/xss.md`

### CWE-89: SQL Injection
**Score: 35.88** | Ver `checks/api/injection.md`

### CWE-416: Use After Free
**Score: 22.13** | C/C++ principalmente.

### CWE-78: OS Command Injection
**Score: 18.58**

```bash
grep -rn "os\.system\|subprocess.*shell=True\|exec(\|execSync\|child_process\." --include="*.{py,js,ts,rb,php}" -n
```

### CWE-20: Improper Input Validation
**Score: 16.35** | Ver `checks/api/input-validation.md`

### CWE-125: Out-of-bounds Read
**Score: 14.60** | C/C++ principalmente.

### CWE-22: Path Traversal
**Score: 14.08**

```bash
grep -rn "path\.join.*req\.\|open.*req\.\|readFile.*req\." --include="*.{js,ts,py}" -n
```

### CWE-352: Cross-Site Request Forgery (CSRF)
**Score: 11.73**

```bash
# Verificar proteção CSRF
grep -rn "csrf\|csurf\|SameSite\|x-csrf" --include="*.{js,ts,py}" -l
grep -rn "csrfToken\|_csrf\|csrf_token" --include="*.{html,jsx,tsx}" -l
```

### CWE-434: Unrestricted Upload of Dangerous File Types
**Score: 10.41**

```bash
grep -rn "multer\|formidable\|upload\|multipart" --include="*.{js,ts}" | grep -v "fileFilter\|mimetype\|allowedTypes"
```

---

## CWEs Relevantes para Web Apps

### CWE-287: Improper Authentication
Falha na verificação de identidade. Ver `checks/authentication/`.

### CWE-306: Missing Authentication for Critical Function
Endpoints sensíveis sem autenticação.

```bash
grep -rn "router\.\(delete\|post.*admin\|put.*user\)" --include="*.{js,ts}" | grep -v "authenticate\|requireAuth\|protect\|guard"
```

### CWE-862: Missing Authorization
Autenticação presente mas sem verificação de autorização.

```bash
grep -rn "findById(req\.params\|findOne(.*req\.params" --include="*.{js,ts}" | grep -v "userId\|user_id\|ownerId"
```

### CWE-918: SSRF
Ver `vulnerabilities/owasp-top-10.md` — A10:2021.

### CWE-798: Hardcoded Credentials
```bash
grep -rn "password\s*=\s*['\"][^'\"]\{8,\}\|api_key\s*=\s*['\"][^'\"]\{8,\}" --include="*.{js,ts,py,go}" -n
```

### CWE-502: Deserialization of Untrusted Data
```bash
grep -rn "pickle\.load\|yaml\.load\b\|eval\b\|unserialize\b\|ObjectInputStream" --include="*.{py,php,java}" -n
```

**yaml.load vs yaml.safe_load:**
```python
# VULNERÁVEL — yaml.load pode executar código Python
yaml.load(user_input)

# SEGURO
yaml.safe_load(user_input)
```

### CWE-611: XML External Entity (XXE)
```bash
grep -rn "DOMParser\|XMLParser\|parseString\|lxml\|ElementTree\|SAXParser" --include="*.{js,ts,py,java}" -n | grep -v "resolve_entities.*False\|no_network.*True"
```

---

## Checklist Resumido

- [ ] CWE-79 (XSS): Output sanitizado
- [ ] CWE-89 (SQLi): Queries parametrizadas
- [ ] CWE-78 (Command Injection): Sem shell=True com input externo
- [ ] CWE-22 (Path Traversal): path.basename() e validação
- [ ] CWE-352 (CSRF): SameSite cookies ou CSRF tokens
- [ ] CWE-434 (File Upload): Validação de tipo e tamanho
- [ ] CWE-287 (Auth Failure): Hashing adequado, rate limiting
- [ ] CWE-862 (Missing Authz): Ownership check em todos os endpoints
- [ ] CWE-798 (Hardcoded Creds): Secrets em variáveis de ambiente
- [ ] CWE-502 (Deserialization): yaml.safe_load, sem pickle de input externo
