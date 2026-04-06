# Security Skills

Skills de segurança para Claude Code. Cobrem auditoria completa e remediação de vulnerabilidades em projetos de software.

---

## Skills disponíveis

### [`security-audit`](./security-audit/)

Auditoria de segurança nível enterprise. Atua como AppSec Engineer, Security Architect, Cloud Security Engineer e Red Team Thinker em uma só skill.

Cobre OWASP Top 10, API Top 10, SANS Top 25 e riscos específicos de vibe coding e código gerado por IA.

```bash
/security-audit              # auditoria completa (16 passos)
/security-audit quick        # scan rápido — secrets e críticos
/security-audit auth         # autenticação e autorização
/security-audit secrets      # credenciais e arquivos sensíveis
/security-audit infra        # Docker, cloud, IAM e CI/CD
/security-audit data         # banco de dados e storage
/security-audit remediation  # priorizar correções de relatório existente
```

---

### [`security-fix`](./security-fix/)

Corrige os problemas encontrados pelo `security-audit`. Pede confirmação antes de cada alteração e mostra o diff antes de aplicar.

```bash
/security-fix critical          # corrigir apenas críticos
/security-fix high              # críticos e altos
/security-fix all               # todos os issues
/security-fix CRIT-001          # issue específico por ID
/security-fix critical --dry-run  # visualizar sem aplicar
```

---

## Fluxo recomendado

```
1. /security-audit deep       → relatório completo
2. /security-fix critical     → corrigir críticos com confirmação
3. /security-audit quick      → verificar se críticos foram resolvidos
4. /security-fix high         → corrigir altos
```

---

## Instalação

Copie a pasta da skill desejada para `~/.claude/skills/security/`:

```bash
# Instalar as duas skills
git clone https://github.com/kleberbernardo/claude-code-skills.git
cp -r claude-code-skills/security ~/.claude/skills/

# Ou apenas uma delas
cp -r claude-code-skills/security/security-audit ~/.claude/skills/security/
```

No Windows (PowerShell):
```powershell
git clone https://github.com/kleberbernardo/claude-code-skills.git
Copy-Item -Recurse claude-code-skills\security $env:USERPROFILE\.claude\skills\
```
