# claude-code-skills

Coleção de skills para Claude Code.

---

## Skills disponíveis

### [`security-audit`](./security-audit/)

Auditoria e correção de segurança nível enterprise. AppSec, Cloud Security, DevSecOps, Infra Security em uma só skill.

Cobre OWASP Top 10, API Top 10, SANS Top 25 e riscos de vibe coding e código gerado por IA.

**Auditoria:**
```bash
/security-audit              # auditoria completa (16 passos)
/security-audit quick        # scan rápido — secrets e críticos
/security-audit auth         # autenticação e autorização
/security-audit secrets      # credenciais e arquivos sensíveis
/security-audit infra        # Docker, cloud, IAM e CI/CD
/security-audit data         # banco de dados e storage
/security-audit remediation  # priorizar correções de relatório existente
```

**Correção:**
```bash
/security-audit fix critical          # corrigir apenas críticos
/security-audit fix high              # críticos e altos
/security-audit fix all               # todos os issues
/security-audit fix CRIT-001          # issue específico por ID
/security-audit fix critical --dry-run  # visualizar sem aplicar
```

---

## Instalação

```bash
git clone https://github.com/kleberbernardo/claude-code-skills.git
cp -r claude-code-skills/security-audit ~/.claude/skills/
```

Windows (PowerShell):
```powershell
git clone https://github.com/kleberbernardo/claude-code-skills.git
Copy-Item -Recurse claude-code-skills\security-audit $env:USERPROFILE\.claude\skills\
```

Reinicie o Claude Code. A skill aparece como `/security-audit`.
