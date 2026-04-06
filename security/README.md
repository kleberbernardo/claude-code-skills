# Security Skill

Skill de segurança para Claude Code. Auditoria completa e remediação de vulnerabilidades em uma única skill.

---

## Skill disponível

### [`security-audit`](./security-audit/)

Auditoria e correção de segurança nível enterprise. Atua como AppSec Engineer, Security Architect, Cloud Security Engineer e Red Team Thinker em uma só skill.

Cobre OWASP Top 10, API Top 10, SANS Top 25 e riscos específicos de vibe coding e código gerado por IA.

#### Modos de auditoria

```bash
/security-audit              # auditoria completa (16 passos)
/security-audit quick        # scan rápido — secrets e críticos
/security-audit auth         # autenticação e autorização
/security-audit secrets      # credenciais e arquivos sensíveis
/security-audit infra        # Docker, cloud, IAM e CI/CD
/security-audit data         # banco de dados e storage
/security-audit remediation  # priorizar correções de relatório existente
```

#### Modos de correção

```bash
/security-audit fix critical          # corrigir apenas críticos
/security-audit fix high              # críticos e altos
/security-audit fix all               # todos os issues
/security-audit fix CRIT-001          # issue específico por ID
/security-audit fix critical --dry-run  # visualizar sem aplicar
```

---

## Fluxo recomendado

```
1. /security-audit deep          → relatório completo
2. /security-audit fix critical  → corrigir críticos com confirmação
3. /security-audit quick         → verificar se críticos foram resolvidos
4. /security-audit fix high      → corrigir altos
```

---

## Instalação

```bash
# Clonar o repositório
git clone https://github.com/kleberbernardo/claude-code-skills.git

# Copiar a skill para o diretório global do Claude Code
cp -r claude-code-skills/security/security-audit ~/.claude/skills/
```

No Windows (PowerShell):
```powershell
git clone https://github.com/kleberbernardo/claude-code-skills.git
Copy-Item -Recurse claude-code-skills\security\security-audit $env:USERPROFILE\.claude\skills\
```

> **Importante:** Copie a pasta `security-audit` diretamente para `~/.claude/skills/`.
> Não crie uma subpasta `security/` dentro de skills — o Claude Code só detecta skills como filhos diretos de `~/.claude/skills/`.

Reinicie o Claude Code após instalar. A skill aparece como `/security-audit` no menu.
