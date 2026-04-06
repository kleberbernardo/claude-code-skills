# Supply Chain Security

Ataques à cadeia de suprimento de software — comprometimento de dependências antes de chegarem ao seu projeto.

---

## Tipos de Ataques de Supply Chain

### 1. Typosquatting
Pacotes com nomes similares aos populares:
- `crossenv` (typo de `cross-env`) — 2017, código malicioso
- `event-stream` — 2018, dependência comprometida
- `colors` e `faker` — 2022, autor sabotou intencionalmente

```bash
# Verificar nomes suspeitos (difíceis de detectar automaticamente)
# Focar em pacotes com poucos downloads mas usados criticamente
cat package.json | python3 -c "
import json, sys
p = json.load(sys.stdin)
deps = {**p.get('dependencies',{}), **p.get('devDependencies',{})}
for name, version in deps.items():
    print(f'{name}: {version}')
" | head -50
```

### 2. Dependency Confusion
Pacotes internos com mesmo nome de pacotes públicos.

```bash
# Verificar se há packages com escopo interno sendo instalados
grep -rn "@company/\|@internal/" package.json
# Se usam npm público (não registry privado), risco de confusion attack
```

### 3. Pacotes Comprometidos Pós-Publicação
O autor original tem conta comprometida e atualiza o pacote com código malicioso.

```bash
# Verificar se há "postinstall" scripts em dependências (podem executar código arbitrário)
cat node_modules/*/package.json 2>/dev/null | python3 -c "
import json, sys
data = sys.stdin.read()
# verificar scripts.postinstall nos pacotes instalados
"

# Via npm
npm ls --json 2>/dev/null | head -5
```

---

## Verificações de Integridade

### Subresource Integrity (SRI) para CDNs

```bash
grep -rn "cdn\|unpkg\|jsdelivr\|cdnjs" --include="*.{html,jsx,tsx}" | grep "src=\|href=" | grep -v "integrity="
```

**Sem SRI:**
```html
<!-- INSEGURO — CDN comprometido pode servir malware -->
<script src="https://cdn.example.com/library.js"></script>

<!-- SEGURO — com SRI -->
<script 
  src="https://cdn.example.com/library.js"
  integrity="sha384-..."
  crossorigin="anonymous">
</script>
```

---

## Checklist

- [ ] Packages publicados por organizações conhecidas e mantidas
- [ ] Subresource Integrity (SRI) para scripts de CDN
- [ ] Registry privado para packages internos (evitar confusion attack)
- [ ] Dependências mínimas — cada dependência é um vetor de ataque
- [ ] `npm audit` no CI/CD
- [ ] Alertas de segurança do GitHub Dependabot habilitados

---

## Red Flags

1. Pacote com nome similar a popular mas de autor desconhecido
2. Scripts de CDN sem SRI em código de produção
3. Sem Dependabot ou similar para alertas de segurança
4. Dezenas de dependências diretas sem revisão (vibe coding acumula deps)
5. Package com poucos downloads mas acesso a funcionalidades críticas
