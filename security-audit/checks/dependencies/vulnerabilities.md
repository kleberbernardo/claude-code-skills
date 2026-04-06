# Vulnerabilidades em Dependências

CVEs conhecidos em pacotes — um dos vetores mais comuns em ataques automatizados.

---

## Verificação Rápida

```bash
# Node.js
cat package.json | grep '"version"'
npm audit --audit-level=high 2>/dev/null | head -50  # se npm disponível

# Python
cat requirements.txt
# pip-audit, safety, snyk

# Go
cat go.sum | wc -l
go list -m all 2>/dev/null | head -20

# Ruby
cat Gemfile.lock | head -50
```

---

## Pacotes com Histórico de CVEs Críticos

### Node.js
```bash
grep -E '"(lodash|moment|axios|log4j|express|jsonwebtoken|node-serialize|serialize-javascript|vm2|eval5)"' package.json
```

| Pacote | CVE Notável | Risco |
|--------|------------|-------|
| `log4js` | Configuração perigosa | RCE em configs específicas |
| `node-serialize` | CVE-2017-5941 | RCE via IIFE em objeto serializado |
| `vm2` | Múltiplos CVEs 2022-2023 | Sandbox escape → RCE |
| `eval` | N/A | Por design executa código |
| `jsonwebtoken` < 9.0 | CVE-2022-23529 | Bypass de verificação |
| `lodash` < 4.17.21 | CVE-2020-8203 | Prototype pollution |

### Python
```bash
grep -E "^(pillow|pyyaml|requests|urllib3|cryptography|paramiko|flask|django|sqlalchemy)" requirements.txt
```

---

## Versões com CVEs Críticos Ativos

**Verificar especificamente:**

```bash
# Extrair versões e comparar manualmente ou via ferramenta
node -e "const p = require('./package.json'); Object.entries({...p.dependencies,...p.devDependencies}).forEach(([k,v]) => console.log(k+'@'+v))"

python3 -c "
import re
with open('requirements.txt') as f:
    for line in f:
        line = line.strip()
        if line and not line.startswith('#'):
            print(line)
"
```

---

## Lock Files

```bash
# Verificar se lock files existem e estão commitados
ls package-lock.json yarn.lock pnpm-lock.yaml requirements.txt Pipfile.lock go.sum Gemfile.lock 2>/dev/null
git ls-files | grep -E "package-lock\.json|yarn\.lock|pnpm-lock\.yaml|go\.sum|Gemfile\.lock"
```

**Lock files não commitados:** Instalações podem pegar versões diferentes com CVEs.

---

## Checklist

- [ ] Lock file commitado (package-lock.json, yarn.lock, etc.)
- [ ] Sem versões com `*` ou `latest` em package.json
- [ ] `npm audit` ou equivalente sem issues críticos/altos
- [ ] Dependências com patches de segurança aplicados
- [ ] Pacotes abandonados (sem manutenção > 2 anos) revisados

---

## Red Flags

1. `"*"` ou `"latest"` em versões de pacote
2. Lock file ausente ou não commitado
3. `node-serialize`, `vm2` em dependências
4. Pacotes com CVE crítico publicado nos últimos 6 meses
5. Dependências diretas com versões muito antigas (> 2 anos sem update)
