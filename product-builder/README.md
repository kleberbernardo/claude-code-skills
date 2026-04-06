# product-builder

Skill completa de construção de produto para Claude Code.

Funciona como um **Product Manager + Tech Lead + Senior Engineer** trabalhando juntos — do zero ao software funcionando, com cada etapa documentada e rastreável.

---

## Como Usar

```bash
/product-builder mvp         # Descoberta e definição do MVP
/product-builder prd         # PRD completo por feature
/product-builder tasks       # Quebra em tasks FE + BE + QA
/product-builder plan <id>   # Plano técnico detalhado
/product-builder execute <id> # Execução cirúrgica da task
/product-builder qa <id>     # Validação com relatório
```

---

## Fluxo

```
Ideia → MVP → PRD → Tasks → Plan → Execute → QA → Feature Done
```

Cada etapa gera artefatos em `.ai/product/` — rastreáveis, versionáveis, reutilizáveis.

---

## O que Cada Modo Faz

| Modo | Input | Output | Onde salva |
|------|-------|--------|-----------|
| `mvp` | Ideia verbal | Documento MVP aprovado | `.ai/product/01-discovery/` |
| `prd` | MVP aprovado | PRD por feature com ACs | `.ai/product/02-prd/` |
| `tasks` | PRD completo | Tasks BE + FE + QA | `.ai/product/03-tasks/` |
| `plan` | Task + codebase | Plano técnico detalhado | `.ai/product/04-plans/` |
| `execute` | Plano aprovado | Código implementado | Projeto + `.ai/product/05-execution/` |
| `qa` | ACs + código | Relatório de QA | `.ai/product/06-qa/` |

---

## Princípios

- **Uma pergunta por vez** — entrevistas de descoberta nunca sobrecarregam
- **Frontend é primeira classe** — plano de FE tão detalhado quanto BE
- **Tasks pequenas e independentes** — máximo 1-2 dias, com ACs claros
- **Escopo cirúrgico** — execução respeita exatamente o plano, sem scope creep
- **Artefatos persistidos** — tudo documentado em `.ai/product/` antes de avançar

---

## Estrutura de Arquivos

```
.claude/skills/product-builder/
  SKILL.md                          ← configuração da skill
  README.md                         ← este arquivo
  core/                             ← fluxo, regras, convenções
  modes/                            ← fluxo detalhado por modo
  planning/                         ← guias de planejamento FE/BE
  product/                          ← metodologia de produto
  templates/                        ← templates de artefatos
  prompts/                          ← perguntas e regras
  examples/                         ← exemplos reais
  knowledge/                        ← princípios e boas práticas
```

---

## Saída no Projeto

```
.ai/product/
  01-discovery/    ← MVP, jornadas, mapa de telas
  02-prd/          ← PRD consolidado + features individuais
  03-tasks/        ← Tasks overview + BE/FE/QA por feature
  04-plans/        ← Planos técnicos por task
  05-execution/    ← Logs de execução
  06-qa/           ← Relatórios de QA
```

---

## Instalação

Copiar a pasta `product-builder` para:
- `~/.claude/skills/product-builder/` — escopo global (todos os projetos)
- `.claude/skills/product-builder/` — escopo local (projeto específico)
