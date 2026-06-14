# Bug Hunter

Skill de **debug e correção disciplinados** para o Claude Code.

Encontra a causa raiz real de um defeito antes de tocar no código, e corrige com cirurgia — não com tentativa e erro. Cobre o caminho prático **stack trace → causa raiz → plano → fix**.

---

## Uso

```bash
# 1. Analisar — descreva o problema ou cole o stack trace
/bug-hunter analyze
# (ex: "ao logar, o usuário cai num loop de redirecionamento. Stack trace: ...")

# 2. Planejar a correção (após confirmar a causa raiz)
/bug-hunter plan 001

# 3. Aplicar a correção + testes (após aprovar o plano)
/bug-hunter fix 001

# Ver todos os bugs e seus estados
/bug-hunter status
```

---

## Fluxo

```
Problema (descrição / stack trace / comportamento errado)
    ↓
/bug-hunter analyze   → entende, reproduz, analisa o código,
                        monta o passo a passo da causa raiz
    ↓  (você confirma: "a análise está correta")
/bug-hunter plan 001  → onde corrigir: arquivo, trecho, por quê
                        + impacto, riscos, estratégia de teste
    ↓  (você aprova o plano)
/bug-hunter fix 001   → aplica a correção mínima + testes, valida
```

Dois **gates** humanos protegem o processo:
- Não há plano sem **causa raiz confirmada** por você.
- Não há código sem **plano aprovado** por você.

E o modo `analyze` é **iterativo**: se você disser que a análise não está correta, passa novos insights e ele aprofunda — até chegar na visão certa.

---

## Quando o problema está ambíguo

Se a skill não conseguir entender o problema ou qual seria o comportamento *correto*, ela **pede** antes de analisar:
- mais detalhes e passos de reprodução;
- o stack trace / erro literal;
- a **spec** ou a **URL** da doc/página do comportamento correto (lida via WebFetch).

Ela nunca adivinha o comportamento esperado para corrigir contra ele.

---

## Artefatos

Cada bug vira um arquivo único que evolui ao longo dos modos:

```
.ai/debug/
  001-login-loop-infinito.md
  002-saldo-negativo-checkout.md
```

Cabeçalho de estado em cada arquivo:

```
Status: investigating | analyzed | planned | fixed
RootCause: unknown | confirmed
Plan: not-started | done
Fix: not-started | done
```

`analyze` escreve Compreensão + Causa Raiz · `plan` anexa o Plano de Correção · `fix` anexa a Correção Aplicada.

---

## Estrutura da skill

```
bug-hunter/
  SKILL.md                      ← ponto de entrada
  README.md                     ← este arquivo
  core/
    bug-flow.md                 ← fluxo completo e gates entre modos
    clarification-rules.md      ← quando/como pedir detalhes, spec, URL
    id-resolution.md            ← resolução de bug-id e auto-descoberta
  modes/
    analyze.md                  ← entender + causa raiz (iterativo)
    plan.md                     ← plano de correção
    fix.md                      ← execução + testes
    status.md                   ← relatório de bugs
  templates/
    analysis-template.md        ← estrutura do arquivo do bug
    fix-plan-template.md        ← seção do plano de correção
  prompts/
    root-cause-rules.md         ← como investigar a causa raiz
    fix-rules.md                ← como corrigir com disciplina + testes
  examples/
    analysis-example.md         ← análise de referência
    fix-plan-example.md         ← plano de referência
  knowledge/
    debugging-principles.md     ← fundamentos de depuração
```

---

## Princípios

- Nunca corrigir sem causa raiz confirmada — sem confirmação, sem plano.
- Nunca declarar causa raiz sem evidência — `arquivo:linha`, não hipótese.
- Nunca tratar sintoma como causa — perguntar "por quê?" até a origem.
- Nunca adivinhar o comportamento correto — pedir spec/URL.
- Sempre mudança mínima — não refatorar o que não causa o bug.
- Sempre cobrir com teste — falha antes, passa depois.
- Iterar a análise até você confirmar — refutação é mais um insight.
