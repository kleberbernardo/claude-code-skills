# Exemplo: Infra Audit

Auditoria focada em Docker, cloud e CI/CD.

---

## Invocação

```
/security-audit infra
```

## Exemplo de Saída

```markdown
# INFRA SECURITY AUDIT — MyApp

## 🔴 CRITICAL

### Docker Socket Montado
- Arquivo: docker-compose.yml:22
- `/var/run/docker.sock:/var/run/docker.sock` no container da app
- Escape de container trivial → acesso root ao host
- Fix: remover completamente — sem justificativa para app de negócio

## 🟠 HIGH

### Porta do Banco Exposta ao Host
- docker-compose.yml:35: `"5432:5432"`
- Banco acessível em localhost:5432 → em cloud pode ser público
- Fix: remover ports do postgres, usar apenas rede interna

### Dockerfile Roda como Root
- Dockerfile:18 — sem diretiva USER
- Container roda como root → qualquer RCE tem acesso root
- Fix: adicionar `USER node` após instalar dependências

### GitHub Actions com pull_request_target Inseguro
- .github/workflows/ci.yml:8
- `on: pull_request_target` + `actions/checkout` com `ref: ${{ github.event.pull_request.head.sha }}`
- Código de fork externo executa com acesso a secrets
- Fix: remover o `ref:` ou migrar para `pull_request`

## 🟡 MEDIUM

### Imagem Base com Latest
- Dockerfile:1 — `FROM node:latest`
- Versão não controlada, pode quebrar ou ter CVEs
- Fix: `FROM node:20-alpine`

### Actions de Terceiros sem Versão Fixada
- `uses: actions/cache@main` (3 ocorrências)
- Versão mutável → supply chain risk
- Fix: usar hash ou versão semântica

## ✅ OK
- Secrets de CI/CD usam ${{ secrets.* }} corretamente
- Nenhuma senha hardcoded em docker-compose
- RDS configurado como não público (terraform)
```
