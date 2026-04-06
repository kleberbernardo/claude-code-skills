# Threat Modeling

Metodologia para modelagem de ameaças aplicada à auditoria de segurança. Baseada em STRIDE, árvores de ataque e análise de impacto de negócio.

---

## STRIDE Framework

Para cada componente do sistema, avaliar cada categoria de ameaça:

### S — Spoofing (Falsificação de Identidade)
**Pergunta:** Um atacante pode se passar por outro usuário, sistema ou componente?

**Vetores comuns:**
- Tokens JWT com algoritmo `none` ou secret fraco
- Session fixation — atacante define session ID antes do login
- OAuth sem state parameter (CSRF em OAuth flow)
- SSRF — servidor age como intermediário para atacante
- IP spoofing em sistemas que confiam em IP

**Verificar no código:**
```bash
grep -rn "trust.*ip\|x-forwarded-for\|remote_addr" --include="*.{js,ts,py,go}"
grep -rn "jwt\.decode\|jwt\.verify" --include="*.{js,ts}"
```

---

### T — Tampering (Adulteração de Dados)
**Pergunta:** Um atacante pode modificar dados em trânsito ou em repouso?

**Vetores comuns:**
- HTTP em vez de HTTPS (MITM possível)
- JWT sem verificação de assinatura
- Parâmetros na URL ou cookies não assinados
- Mass assignment — usuário modifica campos não permitidos
- Falta de integridade em uploads de arquivo

**Verificar:**
```bash
grep -rn "http://" --include="*.{js,ts,py,env}"
grep -rn "verify.*false\|ssl.*false\|tls.*false" --include="*.{js,ts,py,go}"
```

---

### R — Repudiation (Repúdio)
**Pergunta:** Um ator pode negar ter realizado uma ação?

**Vetores comuns:**
- Falta de logs de auditoria
- Logs sem timestamp, user ID, ou IP
- Logs mutáveis (sem append-only ou exportação)
- Sem correlação de eventos entre serviços

**Verificar:**
- Existência de audit trail para operações críticas
- Logs de autenticação (login, logout, falha)
- Logs de operações sensíveis (pagamento, deletar, exportar)

---

### I — Information Disclosure (Divulgação de Informação)
**Pergunta:** Informações confidenciais podem ser acessadas por quem não deveria?

**Vetores comuns:**
- Stack traces em respostas de erro
- Endpoints de debug/health expostos com dados internos
- IDOR — acesso a recursos de outros usuários
- Logs com PII/credenciais
- Source maps em produção
- Directory listing habilitado

**Verificar:**
```bash
grep -rn "console\.error\|traceback\|stack" --include="*.{js,ts,py}" -A 2
grep -rn "debug.*true\|NODE_ENV.*development" --include="*.{env,json}"
```

---

### D — Denial of Service (Negação de Serviço)
**Pergunta:** Um atacante pode tornar o sistema indisponível?

**Vetores comuns:**
- Sem rate limiting em APIs públicas
- Queries sem LIMIT (retorno de datasets enormes)
- ReDoS — regex catastrófico
- Resource exhaustion — uploads sem limite de tamanho
- Sem timeout em chamadas externas
- Algorithmic complexity attacks

**Verificar:**
```bash
grep -rn "find\(\|findAll\|SELECT \*" --include="*.{js,ts,py,go}" | grep -v "LIMIT\|limit\|take\|first"
grep -rn "\.regex\|new RegExp" --include="*.{js,ts}" # verificar ReDoS
```

---

### E — Elevation of Privilege (Escalação de Privilégio)
**Pergunta:** Um usuário pode obter mais permissões do que deveria?

**Vetores comuns:**
- IDOR em endpoints de admin
- Falta de verificação de role em endpoints sensíveis
- Parâmetros de role/admin modificáveis pelo usuário
- JWT com role não verificado server-side
- Bugs de lógica em fluxo de promoção de usuário

**Verificar:**
```bash
grep -rn "isAdmin\|role\|permission\|admin" --include="*.{js,ts,py}" | grep -v "check\|verify\|require\|assert\|guard"
```

---

## Árvores de Ataque

### Árvore 1: Comprometer Conta de Usuário
```
[GOAL] Account Takeover
├── [OR] Credentials
│   ├── Brute force senha (sem rate limit)
│   ├── Credential stuffing (senhas de outros leaks)
│   ├── Password reset abusável
│   └── Phishing + falta de MFA
├── [OR] Session
│   ├── XSS + cookies sem HttpOnly
│   ├── Session fixation
│   └── CSRF em operação sensível
└── [OR] Token
    ├── JWT com secret fraco (brute force offline)
    ├── JWT com algoritmo none
    └── Token vazado em logs/frontend
```

### Árvore 2: Acessar Dados de Outros Usuários
```
[GOAL] Data Exfiltration
├── [OR] Direct Access
│   ├── IDOR em endpoint de usuário
│   ├── IDOR em endpoint de documento
│   └── Multi-tenant bypass
├── [OR] Injection
│   ├── SQL injection → dump de tabela
│   └── NoSQL injection → bypass de filtro
└── [OR] Misconfiguration
    ├── Bucket S3 público
    ├── Endpoint de export sem auth
    └── Database exposta publicamente
```

### Árvore 3: Executar Código no Servidor
```
[GOAL] Remote Code Execution
├── [OR] Direct Injection
│   ├── Command injection em campo de input
│   ├── eval() com input do usuário
│   └── Template injection (SSTI)
├── [OR] File Upload
│   ├── Upload de .php/.py sem validação
│   ├── Upload de arquivo com path traversal
│   └── Upload de arquivo zip com path traversal (ZipSlip)
└── [OR] Dependency
    ├── Vulnerable package com exploit público
    └── Supply chain attack
```

---

## Análise de Impacto de Negócio

Para cada ameaça identificada, avaliar impacto nos 5 eixos:

### 1. Impacto Financeiro
- Acesso a dados de cartão de crédito → fraude direta
- Acesso a sistemas de pagamento → transações não autorizadas
- DDoS → perda de receita por downtime
- Ransomware → pagamento de resgate

### 2. Impacto Regulatório/Legal
- Vazamento de dados pessoais → LGPD/GDPR (multas até 2% faturamento)
- Dados de saúde (PHI) → HIPAA
- Dados de cartão → PCI-DSS
- Dados de crianças → COPPA

### 3. Impacto Reputacional
- Vazamento público de dados → churn de clientes
- Defacement de site → credibilidade
- Phishing via domínio comprometido → confiança

### 4. Impacto Operacional
- Perda de disponibilidade do serviço
- Corrupção de dados
- Comprometimento de integridade de dados

### 5. Impacto de Privacidade
- Exposição de PII (nome, email, CPF, endereço)
- Exposição de dados sensíveis (saúde, religião, orientação)
- Exposição de dados financeiros

---

## Threat Actors

### Atacante Externo Não Autenticado
**Motivação:** Acesso inicial, dados sem custo, vandalismo
**Capacidade:** Script kiddie a avançado
**Vetores principais:** OWASP Top 10, CVEs em dependências, secrets expostos

### Usuário Autenticado Malicioso
**Motivação:** Dados de concorrentes, vantagem competitiva, fraude
**Capacidade:** Conhece a aplicação, usuário legítimo
**Vetores principais:** IDOR, privilege escalation, mass assignment

### Atacante com Acesso Interno (Insider Threat)
**Motivação:** Dados de clientes, código-fonte, sabotagem
**Capacidade:** Conhecimento interno, credenciais legítimas
**Vetores principais:** Exfiltração via API, acesso direto a banco

### Bot / Automação
**Motivação:** Credential stuffing, scraping, fraude em escala
**Capacidade:** Volume alto, sem limitações humanas
**Vetores principais:** Login endpoints, APIs de criação de conta

### Estado-Nação / APT
**Motivação:** Espionagem, sabotagem, propriedade intelectual
**Capacidade:** Alta, persistência, recursos ilimitados
**Vetores principais:** Supply chain, zero-days, spear phishing

---

## DREAD Scoring (Complementar ao CVSS)

Para priorização rápida:

| Categoria | 1 (Baixo) | 5 (Médio) | 10 (Alto) |
|-----------|-----------|-----------|-----------|
| **D**amage | Dados mínimos expostos | Dados de usuário | Dados financeiros/RCE |
| **R**eproducibility | Difícil reproduzir | Reproduzível com esforço | 100% reproduzível |
| **E**xploitability | Requer conhecimento avançado | Script disponível | Exploração via browser |
| **A**ffected Users | 1 usuário | Grupo de usuários | Todos usuários |
| **D**iscoverability | Muito difícil descobrir | Possível com análise | Óbvio/documentado |

**Score final:** Média dos 5 valores → 1-3: Low, 4-6: Medium, 7-8: High, 9-10: Critical
