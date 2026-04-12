# SDD Builder

Skill de Spec-Driven Development para o Claude Code.

Transforma uma ideia em documentação estruturada, completa e executável via entrevista interativa → PRD → Design Técnico → Tasks.

---

## Uso

Todos os modos fazem **auto-descoberta** dos artefatos em `.ai/product/` — não é necessário passar caminhos de arquivo.

### Produto novo (greenfield)

```bash
/sdd-builder spec     # entrevista → gera prd.md + ux.md
/sdd-builder design   # lê prd.md + ux.md automaticamente → gera design.md
/sdd-builder tasks    # lê prd.md + design.md + ux.md automaticamente → gera tasks/
```

### Feature em produto existente (incremental)

```bash
/sdd-builder feature        # descobre prd.md existente → entrevista da feature → gera features/001-nome.md
/sdd-builder design 001     # atualiza design.md com a feature 001
/sdd-builder tasks 001      # gera tasks da feature 001 (numeração continuada)
```

---

## Modos

| Modo | Quando usar | Entrada | Saída |
|------|------------|---------|-------|
| `spec` | Produto novo, do zero | Entrevista interativa | `prd.md` + `ux.md` (opcional) |
| `feature` | Feature em produto existente | Entrevista + artefatos existentes | `features/NNN.md` + atualiza `prd.md`, `ux.md` |
| `design` | Após spec ou feature | `prd.md` + `ux.md` opcional | `design.md` (criado ou atualizado) |
| `tasks` | Após design | `prd.md` + `design.md` + `ux.md` opcional | `tasks/*.md` (criadas ou adicionadas) |

---

## Fluxos

### Greenfield

```
Ideia
  ↓
/sdd-builder spec
  ↓ entrevista em 5 fases (19 blocos) + fase UX visual opcional
  ↓ auto-salva prd.md + ux.md em .ai/product/
  ↓
/sdd-builder design
  ↓ auto-descobre prd.md + ux.md
  ↓ verifica versões mais recentes da stack via WebSearch
  ↓ auto-salva design.md
  ↓
/sdd-builder tasks
  ↓ auto-descobre prd.md + design.md + ux.md
  ↓
tasks/001-nome.md
tasks/002-nome.md
  ↓
Execução com /rpi-builder
```

### Incremental (feature em produto existente)

```
Produto existente + nova necessidade
  ↓
/sdd-builder feature
  ↓ auto-descobre prd.md + ux.md + design.md existentes
  ↓ entrevista da feature (7 blocos) + UX delta
  ↓ auto-salva features/001-nome.md
  ↓ atualiza prd.md (escopo + user stories)
  ↓ atualiza ux.md (novas telas/componentes — se houver)
  ↓
/sdd-builder design 001
  ↓ auto-descobre todos artefatos + usa feature 001
  ↓ atualiza design.md in-place, marca [NOVO - feature 001]
  ↓
/sdd-builder tasks 001
  ↓ auto-descobre todos artefatos + usa feature 001
  ↓ numeração sequencial a partir da última task existente
tasks/009-nome.md
tasks/010-nome.md
  ↓
Execução com /rpi-builder
```

---

## Estrutura de output

```
.ai/product/
  prd.md              ← O QUÊ e PARA QUEM (gerado por spec)
  ux.md               ← COMO PARECE (gerado por spec, Fase 5 — opcional)
  design.md           ← COMO FUNCIONA (gerado/atualizado por design)
  features/
    001-nome.md       ← FEATURE INCREMENTAL (gerado por feature)
    002-nome.md
  tasks/
    001-setup.md      ← IMPLEMENTAÇÃO (gerado por tasks)
    002-schema.md
    003-auth.md
    ...
```

### Separação de responsabilidades

| Artefato | Contém | Não contém |
|----------|--------|-----------|
| `prd.md` | Requisitos, user stories, regras de negócio, fluxos | Visual, arquitetura, código |
| `ux.md` | Cores, tipografia, referências, telas-chave, componentes | Requisitos funcionais, arquitetura |
| `design.md` | Stack, módulos, modelo de dados, endpoints, autenticação | Requisitos de produto, visual |
| `features/NNN.md` | Spec focado da feature, integração com produto existente | Stack, visual global |
| `tasks/*.md` | Implementação passo a passo, critérios de conclusão | Requisitos, decisões de design |

---

## O que o entrevistador faz

### No modo `spec`

- Anuncia upfront que auth/cadastro/recuperação de senha já entram automaticamente
- Conduz entrevista em **5 fases**: fundação → produto em detalhe → regras → qualidade → UX visual
- **Uma pergunta por vez** — nunca questionários
- Aprofunda sem aceitar respostas vagas ("é simples", "igual ao Notion")
- Para referências visuais: extrai características conhecidas do produto citado e valida
- Para URLs: usa WebFetch para analisar e descreve o que viu
- Para screenshots: lê a imagem e descreve visualmente

### No modo `feature`

- Se `prd.md` existe: lê e usa como contexto imediatamente
- Se não existe: entrevista de contexto do produto até estar claro (sem limite de trocas)
- **7 blocos focados** no delta da feature (não repete análise do produto inteiro)
- Entrevista UX incremental: só novas telas e novos componentes
- Atualiza artefatos cirurgicamente: nunca reescreve seções sem alteração

### No modo `design`

- Verifica **versões mais recentes** de todos os pacotes via WebSearch antes de especificar
- Nunca usa versão de memória de treinamento — sempre busca a latest stable
- Formato obrigatório: `pacote@versão` (ex: `next@15.2.1`)
- No cenário incremental: lê design existente, adiciona marcado com `[NOVO - feature NNN]`

---

## Estrutura da skill

```
sdd-builder/
  SKILL.md                    ← ponto de entrada + allowed-tools
  README.md                   ← este arquivo
  core/
    interview-rules.md        ← protocolo: uma pergunta por vez + referências visuais
    scope-rules.md            ← como definir e proteger escopo
    output-conventions.md     ← convenções de nomes e paths
  modes/
    spec.md                   ← fluxo do modo SPEC (produto novo)
    feature.md                ← fluxo do modo FEATURE (incremental)
    design.md                 ← fluxo do modo DESIGN
    tasks.md                  ← fluxo do modo TASKS
  templates/
    prd-template.md           ← PRD (19 seções)
    feature-template.md       ← Feature Spec (12 seções)
    ux-template.md            ← UX Visual (11 seções)
    design-template.md        ← Design Técnico
    task-template.md          ← Task individual
  prompts/
    discovery-questions.md    ← banco de perguntas por categoria (incl. UX visual)
    design-rules.md           ← regras para o design técnico
    task-rules.md             ← regras para criação de tasks
  examples/
    prd-example.md            ← PRD de referência completo
    tasks-example.md          ← Task de referência detalhada
  knowledge/
    sdd-principles.md         ← fundamentos do Spec-Driven Development
    rpi-compatibility.md      ← como preparar specs para agentes RPI
```

---

## Princípios

- **Uma pergunta por vez** na entrevista — nunca questionários
- **Nunca inventar escopo** — apenas o que o usuário confirmou explicitamente
- **Premissas sempre marcadas** com `[ASSUMIDO: ...]`
- **Versões sempre verificadas** — WebSearch antes de especificar qualquer pacote
- **Artefatos vivos** — `design.md` e `ux.md` são atualizados in-place, não fragmentados
- **Tasks auto-suficientes** — executáveis por agentes RPI sem dúvidas
- **Clareza antes de execução** — documento curto e claro > longo e confuso
