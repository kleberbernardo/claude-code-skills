# OWASP Top 10 — 2021

Guia de referência para as 10 vulnerabilidades mais críticas em aplicações web.

---

## A01: Broken Access Control (Controle de Acesso Quebrado)
**Movimento: #5 → #1 (mais crítica)**

**O que é:** Usuários podem agir além das permissões pretendidas.

**Incluindo:**
- IDOR (Insecure Direct Object Reference)
- Bypass de controle de acesso por modificação de URL/estado
- Acesso a endpoints de admin sem verificação
- CORS misconfiguration
- Força bruta em endpoints protegidos

**Verificar:** `checks/authorization/idor.md`, `checks/authorization/rbac.md`, `checks/authorization/multi-tenant.md`

---

## A02: Cryptographic Failures (Falhas Criptográficas)
**Movimento: #3 → #2**

**O que é:** Exposição de dados sensíveis por criptografia inadequada ou ausente.

**Incluindo:**
- Dados em trânsito sem TLS
- Senhas com MD5/SHA1 (ou sem hash)
- Dados sensíveis em texto plano no banco
- Geração de números aleatórios não-criptográficos
- Chaves criptográficas hardcoded ou fracas

**Verificar:** `checks/authentication/hashing.md`, `checks/database/encryption.md`

---

## A03: Injection
**Movimento: #1 → #3**

**O que é:** Dados não confiáveis enviados para interpretador como parte de comando ou query.

**Incluindo:**
- SQL Injection
- NoSQL Injection
- OS Command Injection
- LDAP Injection
- Template Injection (SSTI)
- XPath Injection

**Verificar:** `checks/api/injection.md`

---

## A04: Insecure Design (Design Inseguro)
**Novo em 2021**

**O que é:** Falhas de design e arquitetura que não podem ser corrigidas apenas com implementação.

**Incluindo:**
- Ausência de threat modeling
- Falta de limites de negócio (ex: taxa de requisição, valor máximo de transação)
- Fluxos de negócio manipuláveis
- Falta de isolamento entre tenants

**Verificar:** `core/threat-modeling.md`, `checks/backend/business-logic.md`

---

## A05: Security Misconfiguration (Configuração de Segurança Incorreta)
**Movimento: #6 → #5**

**O que é:** Configurações padrão inseguras, incompletas ou abertas demais.

**Incluindo:**
- Cloud misconfiguration (S3 público, Security Groups abertos)
- Headers de segurança ausentes
- Contas padrão ativas
- Stack traces em produção
- Features desnecessárias habilitadas
- CORS permissivo

**Verificar:** `checks/infrastructure/`, `checks/api/cors.md`, `checks/backend/error-handling.md`

---

## A06: Vulnerable and Outdated Components
**Movimento: #9 → #6**

**O que é:** Componentes com vulnerabilidades conhecidas.

**Incluindo:**
- Dependências com CVEs
- Sistema operacional desatualizado
- Frameworks sem patches
- Libraries abandonadas

**Verificar:** `checks/dependencies/vulnerabilities.md`, `checks/dependencies/supply-chain.md`

---

## A07: Identification and Authentication Failures
**Movimento: #2 → #7**

**O que é:** Falhas em confirmar identidade do usuário.

**Incluindo:**
- Senhas fracas ou padrão
- Brute force sem proteção
- Session fixation
- JWT inseguro
- Falta de MFA
- Credenciais em URLs
- Expiração inadequada de sessão

**Verificar:** `checks/authentication/`

---

## A08: Software and Data Integrity Failures
**Novo em 2021 (engloba Insecure Deserialization)**

**O que é:** Código ou dados que não verificam integridade.

**Incluindo:**
- Desserialização insegura
- CI/CD sem verificação de integridade
- Auto-update sem verificação
- Supply chain attacks
- Plugins sem verificação

**Verificar:** `checks/dependencies/supply-chain.md`, `checks/ci-cd/`

---

## A09: Security Logging and Monitoring Failures
**Movimento: #10 → #9**

**O que é:** Ausência de logs e monitoramento impede detecção de ataques.

**Incluindo:**
- Sem log de eventos de autenticação
- Logs insuficientes para investigação forense
- Alertas sem monitoramento
- Logs apenas locais (sem exportação)

**Verificar:**
```bash
grep -rn "console\.log\|logger\.\|logging\." --include="*.{js,ts,py}" -l
grep -rn "loginAttempt\|authFail\|accessDenied\|unauthorized" --include="*.{js,ts,py}" -l
```

---

## A10: Server-Side Request Forgery (SSRF)
**Novo em 2021**

**O que é:** Servidor faz request para destino controlado pelo atacante, potencialmente acessando rede interna.

**Incluindo:**
- URL de usuário acessada pelo servidor
- Webhooks sem validação de destino
- PDF/image rendering com URL do usuário
- Importação de dados de URL

**Buscar:**
```bash
grep -rn "fetch\|axios\|http\.get\|requests\.get\|urllib\.request" --include="*.{js,ts,py}" | grep "req\.\|body\.\|params\.\|url\b"
```

**Proteção:**
```javascript
// Validar URL antes de fazer request
function validateUrl(url: string): URL {
  const parsed = new URL(url)
  if (!['https:', 'http:'].includes(parsed.protocol)) throw new Error('Invalid protocol')
  
  // Bloquear IPs internos
  const privateRanges = ['127.', '10.', '192.168.', '172.16.', '169.254.']
  if (privateRanges.some(r => parsed.hostname.startsWith(r))) {
    throw new Error('Private IP not allowed')
  }
  
  return parsed
}
```
