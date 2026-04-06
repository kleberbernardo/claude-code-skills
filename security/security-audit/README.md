# security-audit

Skill de auditoria de segurança completa nível enterprise para Claude Code.

Atua como um time completo de segurança: AppSec Engineer, Security Architect, Cloud Security Engineer, Red Team Thinker e Auditor de Produção — tudo em uma única skill.

Cobre desde problemas básicos até os mais avançados, com atenção especial a vulnerabilidades comuns em projetos feitos com IA (vibe coding), low-code e automações.

---

## Como usar

```bash
/security-audit              # auditoria completa (16 passos)
/security-audit quick        # scan rápido — secrets, credenciais, críticos óbvios
/security-audit auth         # foco em autenticação e autorização
/security-audit secrets      # foco em credenciais e arquivos sensíveis
/security-audit infra        # foco em Docker, cloud, IAM e CI/CD
/security-audit data         # foco em banco de dados e storage
/security-audit remediation  # analisa relatório existente e prioriza correções
```

---

## O que audita

- Secrets e credenciais expostos (`.env`, tokens hardcoded, histórico git)
- Autenticação: hashing, JWT, OAuth, sessão, MFA, brute force
- Autorização: IDOR, RBAC, multi-tenant, RLS
- API: injeção (SQL, NoSQL, Command), CORS, rate limiting, validação de input
- Frontend: XSS, localStorage, secrets no bundle
- Backend: lógica de negócio, validação server-side, error handling
- Banco de dados: permissões, criptografia, queries inseguras
- Storage: buckets públicos, controle de acesso
- Infraestrutura: Docker, Nginx, cloud (AWS/GCP/Azure), IAM
- CI/CD: GitHub Actions, secrets em pipelines, supply chain
- Dependências: CVEs conhecidos, typosquatting

---

## Formato de saída

Todo relatório segue a estrutura:

```
Executive Summary
Critical Issues   (CVSS ≥ 9.0)
High Issues       (CVSS 7.0–8.9)
Medium Issues     (CVSS 4.0–6.9)
Low Issues        (CVSS 0.1–3.9)
Informational
Remediation Roadmap
```

Cada issue inclui: título, severidade, confiança, evidência (com secrets mascarados), cenário de exploração, impacto de negócio, correção recomendada e prioridade.

---

## Skill complementar

**`/security-fix`** — corrige os problemas encontrados, pedindo confirmação antes de cada alteração.

```bash
/security-fix critical       # corrigir apenas críticos
/security-fix high           # críticos e altos
/security-fix all            # todos os issues
/security-fix CRIT-001       # issue específico
/security-fix critical --dry-run  # visualizar correções sem aplicar
```

---

## Estrutura da skill

```
security-audit/
├── SKILL.md                     # entrada principal
├── core/                        # motor de análise e fluxo
├── checks/                      # checklists por categoria
│   ├── repository/
│   ├── authentication/
│   ├── authorization/
│   ├── api/
│   ├── frontend/
│   ├── backend/
│   ├── database/
│   ├── storage/
│   ├── infrastructure/
│   ├── ci-cd/
│   └── dependencies/
├── vulnerabilities/             # OWASP Top 10, API Top 10, SANS Top 25
├── playbooks/                   # guias de resposta e hardening
├── reports/                     # templates e modelo de severidade
├── examples/                    # exemplos de saída por modo
└── knowledge/                   # padrões seguros, anti-patterns, riscos de AI
```
