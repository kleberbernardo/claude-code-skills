# claude-code-skills

Coleção de skills para Claude Code.

---

## Skills disponíveis

| Skill | Descrição |
|-------|-----------|
| [`security-audit`](./security-audit/) | Auditoria e correção de segurança nível enterprise |

---

## Instalação

Copie a pasta da skill desejada direto para `~/.claude/skills/`:

```bash
git clone https://github.com/kleberbernardo/claude-code-skills.git
cp -r claude-code-skills/<nome-da-skill> ~/.claude/skills/
```

Windows (PowerShell):
```powershell
git clone https://github.com/kleberbernardo/claude-code-skills.git
Copy-Item -Recurse claude-code-skills\<nome-da-skill> $env:USERPROFILE\.claude\skills\
```

Reinicie o Claude Code após instalar.
