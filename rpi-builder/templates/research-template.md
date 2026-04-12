# Research - Task [NNN]
<!-- Gerado por /rpi-builder research -->
<!-- Data: YYYY-MM-DD -->

---

## 1. Referências

- Task: .ai/product/tasks/[NNN-task-name].md
- PRD: .ai/product/prd.md
- Design: .ai/product/design.md

---

## 2. Resumo da Tarefa

<!-- O que a task pede, em 3–5 frases. Não copiar — interpretar e resumir o que foi entendido. -->

---

## 3. Objetivo da Análise

<!-- O que esta pesquisa precisa responder para que o plano possa ser gerado. -->

Esta análise visa responder:
- Quais arquivos do projeto estão envolvidos?
- Quais padrões já existem e devem ser seguidos?
- Qual é o estado atual das partes do sistema que serão afetadas?
- Quais são os riscos ou conflitos de implementação?

---

## 4. Arquivos Relevantes Encontrados

<!-- Listar com paths completos. Para cada arquivo: uma linha descrevendo o que é e por que é relevante. -->

| Arquivo | Status | Relevância |
|---------|--------|-----------|
| `src/[caminho]/[arquivo]` | Existe | [Por que é relevante para esta task] |
| `src/[caminho]/[arquivo]` | Não existe ainda | [Precisará ser criado] |

---

## 5. Componentes / Módulos Envolvidos

<!-- Módulos, serviços, classes, funções que serão tocados ou que impactam esta task. -->

### [Módulo/Componente 1]
- **Localização:** `src/[caminho]/`
- **Responsabilidade:** [O que faz atualmente]
- **Relação com a task:** [Como esta task o afeta ou o usa]

### [Módulo/Componente 2]
- **Localização:** `src/[caminho]/`
- **Responsabilidade:** [O que faz atualmente]
- **Relação com a task:** [Como esta task o afeta ou o usa]

---

## 6. Estado Atual do Sistema

<!-- Descrição factual do que existe hoje. Sem opiniões. Sem "deveria". Apenas "é". -->

### O que já existe e pode ser reaproveitado

- [Função/módulo/tipo existente que a task pode usar diretamente]
- [Padrão estabelecido que a implementação deve seguir]

### O que existe e precisará ser modificado

- [Arquivo/função que precisará de mudança e o motivo]

### O que não existe e precisará ser criado

- [O que está ausente e será criado na implementação]

---

## 7. Dependências Identificadas

<!-- Dependências reais encontradas no código, não apenas as listadas na task. -->

### Dependências internas (outros módulos do projeto)

| Módulo/Arquivo | Tipo de dependência | Observação |
|---------------|--------------------| -----------|
| `src/[caminho]` | Import direto | [Para que será usado] |
| `src/[caminho]` | Chamada indireta | [Como afeta a task] |

### Dependências externas (pacotes)

| Pacote | Versão atual | Uso na task |
|--------|-------------|-------------|
| `[pacote]` | `[versão]` | [Como será usado] |

---

## 8. Regras e Contratos Relevantes

<!-- Tipos, interfaces e contratos do Design que esta task deve respeitar. Copiar literalmente. -->

### Tipos relevantes encontrados no projeto

```typescript
// Existente em src/[caminho]
[tipo ou interface atual]
```

### Contratos definidos no Design (seção X)

```typescript
// Copiado de design.md
[tipo ou interface do design]
```

### Regras de negócio aplicáveis (PRD seção X)

- **RN-[ID]:** [Regra copiada do PRD]

---

## 9. Padrões Existentes no Projeto

<!-- Como o projeto já faz coisas similares. A implementação deve seguir estes padrões. -->

### Estrutura de arquivos

```
src/
  modules/
    [exemplo-modulo]/       ← padrão existente para módulos
      [nome].service.ts
      [nome].handler.ts
      [nome].types.ts
```

### Tratamento de erros

```typescript
// Padrão encontrado em src/[caminho]/[arquivo].ts
[exemplo de como erros são tratados no projeto]
```

### Acesso ao banco de dados

```typescript
// Padrão encontrado em src/[caminho]/[arquivo].ts
[exemplo de como o ORM/DB é usado no projeto]
```

### Autenticação/autorização (se relevante)

```typescript
// Padrão encontrado em src/[caminho]/[arquivo].ts
[como autenticação é verificada nos endpoints existentes]
```

---

## 10. Impacto Esperado da Mudança

<!-- O que muda no sistema quando esta task for implementada. Factual, não especulativo. -->

### Arquivos afetados

- `src/[caminho]` — [como muda]
- `src/[caminho]` — [como muda]

### Fluxos afetados

- [Fluxo técnico do Design que esta task ativa ou modifica]

### Módulos que precisarão ser notificados / atualizados

- [Módulo X] — [por que é afetado]

---

## 11. Pontos de Atenção

<!-- Detalhes não óbvios que o planejamento precisa considerar. Sem opiniões — apenas fatos e observações. -->

- [Ponto 1: algo não óbvio sobre o estado atual do código]
- [Ponto 2: comportamento existente que pode interferir]
- [Ponto 3: detalhe de configuração ou ambiente relevante]

---

## 12. Riscos e Conflitos Possíveis

<!-- Riscos baseados em evidências do código, não hipotéticos abstratos. -->

| Risco | Evidência no código | Impacto | Observação para o plano |
|-------|--------------------| --------|------------------------|
| [Risco específico] | `src/[arquivo]:linha` | [Alto/Médio/Baixo] | [O que o plano precisa endereçar] |

---

## 13. Lacunas ou Dúvidas Encontradas

<!-- O que o research não conseguiu responder. O que o plano precisará decidir ou esclarecer. -->

| # | Lacuna | Impacto no planejamento |
|---|--------|------------------------|
| 1 | [Informação não encontrada ou ambígua] | [O que bloqueia ou pode mudar] |

Se não houver lacunas: declarar explicitamente "Nenhuma lacuna identificada."

---

## 14. Resumo Final para Planejamento

<!-- Síntese objetiva do que o modo plan precisa saber. Este é o único resumo — o plano vai ler esta seção primeiro. -->

**Para implementar esta task:**
- Seguir o padrão [X] já existente em [localização]
- Criar [N] arquivo(s) novo(s): [lista]
- Modificar [N] arquivo(s) existente(s): [lista]
- Atenção especial para: [principal risco ou ponto de atenção]
- Lacunas a resolver no plan: [lista ou "nenhuma"]
