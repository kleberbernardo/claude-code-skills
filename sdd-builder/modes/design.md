# Modo DESIGN — SDD Builder

Gerar design técnico completo a partir do PRD.

---

## Objetivo

Traduzir requisitos de produto em arquitetura técnica concreta, sem lacunas, pronta para ser quebrada em tasks executáveis.

---

## Pré-requisitos

- `.ai/product/prd.md` deve existir e estar completo
- Se não existir: interromper e orientar: `Execute /sdd-builder spec primeiro para gerar o PRD.`

---

## Fluxo de Execução

```
1. Ler .ai/product/prd.md completamente
2. Identificar gaps técnicos e decisões implícitas
3. Estruturar o design seguindo templates/design-template.md
4. Salvar em .ai/product/design.md
5. Confirmar e indicar próximo passo
```

---

## Passo 1: Leitura e Análise do PRD

Ler o PRD completo antes de gerar qualquer conteúdo.

Extrair:
- Todos os requisitos funcionais
- Todas as regras de negócio
- Todos os fluxos principais
- Todas as integrações mencionadas
- Todas as restrições técnicas
- Edge cases documentados

---

## Passo 2: Mapeamento de Decisões Técnicas

Para cada seção do design, mapear os requisitos correspondentes do PRD:

| Seção do Design | Fontes no PRD |
|----------------|--------------|
| Stack | Restrições técnicas (seção 12) |
| Módulos | Requisitos funcionais (seção 8) + Fluxos (seção 6) |
| Modelo de Dados | Entidades implícitas nos fluxos e regras de negócio |
| Endpoints | Fluxos principais (seção 6) |
| Autenticação | Tipos de usuário (seção 3) + Regras de acesso (seção 7) |
| Integrações | Restrições e integrações (seção 12) |

Se houver decisão técnica sem correspondência no PRD: registrar em "Itens em Aberto".

---

## Passo 3: Geração do Design

Usar `templates/design-template.md` como estrutura base.

### Regras por seção:

**Seção 3 — Stack e Dependências**
- Listar pacotes com versão quando relevante
- Justificar escolhas não óbvias
- Se o usuário especificou a stack no PRD: usar exatamente o que foi especificado
- Se não especificou: usar padrões modernos e justificar

**Seção 4 — Arquitetura da Solução**
- Diagrama em texto (ASCII ou Mermaid) quando a relação entre componentes for complexa
- Descrever camadas: apresentação, aplicação, domínio, infraestrutura (quando aplicável)

**Seção 5 — Módulos e Responsabilidades**
- Cada módulo deve ter: nome, responsabilidade única, dependências
- Evitar módulos "Utils" ou "Helpers" genéricos — ser específico

**Seção 6 — Modelo de Dados**
- Definir todas as entidades identificadas no PRD
- Campos com tipos, nullability e descrição breve
- Relacionamentos explícitos
- Índices estratégicos

**Seção 7 — Contratos e Interfaces**
- Tipos TypeScript ou equivalente na linguagem da stack
- Contratos de entrada e saída para operações principais

**Seção 8 — Endpoints / Actions / Events**
- Para cada operação: método, path, request body, response, erros possíveis
- Agrupados por módulo/domínio

**Seção 9 — Fluxos Técnicos**
- Traducão técnica dos fluxos do PRD
- Sequência de chamadas, validações, efeitos colaterais

**Seção 10 — Autenticação e Autorização**
- Mecanismo de autenticação (JWT, session, OAuth, etc.)
- Matriz de permissões por role/tipo de usuário
- Endpoints protegidos vs. públicos

**Seção 16 — Trade-offs e Decisões Técnicas**
- Para cada decisão não óbvia: opção escolhida, alternativas consideradas, motivo
- Sem justificativas vagas como "mais fácil" — ser específico

---

## Passo 4: Validação Antes de Salvar

Verificar antes de salvar:

- [ ] Todos os requisitos funcionais do PRD têm correspondência no design
- [ ] Todos os fluxos do PRD têm fluxo técnico correspondente
- [ ] Todos os tipos de usuário têm regras de autorização definidas
- [ ] Modelo de dados cobre todas as entidades dos fluxos
- [ ] Endpoints cobrem todos os fluxos principais
- [ ] Nenhum módulo inventado sem base no PRD
- [ ] Itens em Aberto registram decisões pendentes

---

## Passo 5: Salvar e Confirmar

1. Salvar em `.ai/product/design.md`
2. Exibir confirmação:

```
✅ Design técnico salvo em .ai/product/design.md

Próximo passo: /sdd-builder tasks @.ai/product/prd.md @.ai/product/design.md
```
