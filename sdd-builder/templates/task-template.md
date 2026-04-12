# Task [NNN] — [Nome da tarefa]
<!-- Gerado por /sdd-builder tasks -->
<!-- Referência PRD: [seção relevante] -->
<!-- Referência Design: [módulo/seção relevante] -->

---

## 1. Objetivo

<!-- O que esta task entrega ao final. Uma frase clara e verificável. -->

[Implementar / Criar / Configurar] [o quê] para que [resultado final verificável].

---

## 2. Contexto

<!-- Por que esta task existe. Conectar ao PRD e ao Design. Ajuda o executor a entender o propósito antes de implementar. -->

Esta task implementa [requisito/fluxo do PRD] conforme definido na seção [X] do Design Técnico.

[Breve explicação do contexto de negócio e técnico relevante para esta task.]

**Decisões de design que afetam esta task:**
- [Decisão relevante do design técnico]

---

## 3. Dependências

<!-- Tasks que devem estar concluídas antes de iniciar esta. -->

| Task | Motivo |
|------|--------|
| [NNN - nome] | [Por que deve estar pronta primeiro] |

**Sem dependências:** [Se não houver dependências, declarar explicitamente]

---

## 4. Escopo da Tarefa

<!-- O que esta task faz. Específico e completo. -->

Esta task inclui:
- [ ] [Componente/ação 1]
- [ ] [Componente/ação 2]
- [ ] [Componente/ação 3]

---

## 5. Fora de Escopo

<!-- O que explicitamente NÃO deve ser feito nesta task. Evita scope creep. -->

- [Item que parece relacionado mas não é desta task]
- [Melhoria que seria natural mas não foi especificada]
- [Feature adjacente que pertence a outra task]

---

## 6. Arquivos Envolvidos

<!-- Paths completos a partir da raiz do projeto. -->

| Arquivo | Status | Descrição |
|---------|--------|-----------|
| `src/[caminho]/[arquivo].[ext]` | [novo] | [O que contém] |
| `src/[caminho]/[arquivo].[ext]` | [modificar] | [O que muda] |

---

## 7. Componentes Impactados

<!-- Outros módulos, serviços ou partes do sistema que são afetados ou precisam ser notificados. -->

- **[Módulo X]:** [Como é impactado]
- **[Módulo Y]:** [Como é impactado]

---

## 8. Contratos e Regras Relevantes

<!-- Copiar literalmente do Design Técnico os tipos, interfaces e regras de negócio relevantes. Não resumir. -->

### Tipos e Interfaces

```typescript
// Copiado de design.md — Seção 7

type [Nome] = {
  [campo]: [tipo];
}
```

### Regras de Negócio Aplicáveis

- **RN-[ID]:** [Condição → resultado — copiado do PRD]
- **RN-[ID]:** [Condição → resultado]

### Endpoint / Action

```
[MÉTODO] [/path]

Request: { [campo]: [tipo] }
Response: { [campo]: [tipo] }
Erros: [código] → [condição]
```

---

## 9. Passos de Implementação Sugeridos

<!-- Sequência concreta de implementação. Cada passo é acionável. -->

1. [Ação específica: "Criar arquivo `src/[path]/[file].ts` com a função `[nomeFuncao]` que recebe `[param]` e retorna `[tipo]`"]
2. [Ação específica: "Adicionar schema `[nome]` em `src/db/schema.ts` com os campos: [campo1], [campo2]"]
3. [Ação específica: "Implementar validação de [regra RN-001] no service antes de persistir"]
4. [Ação específica: "Criar endpoint `[MÉTODO] [/path]` em `src/routes/[arquivo].ts`"]
5. [Ação específica: "Adicionar middleware de autenticação ao endpoint (requer [role])"]
6. [Ação específica: "Criar componente `[NomeComponente]` em `src/components/[path].tsx`"]

---

## 10. Critérios de Conclusão

<!-- Verificáveis. Cada item é sim/não. A task só está pronta quando todos forem verdadeiros. -->

- [ ] [Funcionalidade X] funciona conforme fluxo descrito no PRD seção [N]
- [ ] Regra de negócio RN-[ID] é aplicada e testada
- [ ] Endpoint retorna [código] para [condição de erro]
- [ ] Todos os testes obrigatórios passam
- [ ] Sem erros de TypeScript / lint
- [ ] [Validação manual: ação específica retorna resultado esperado]

---

## 11. Testes Obrigatórios

### 11.1 Testes Unitários

<!-- Funções/classes específicas com casos específicos. Não "testar o módulo". -->

| Função/Classe | Caso de Teste | Entrada | Resultado Esperado |
|--------------|--------------|---------|-------------------|
| `[nomeFuncao]` | [cenário happy path] | `[input]` | `[output]` |
| `[nomeFuncao]` | [cenário de erro] | `[input inválido]` | `throw [ErrorType]` |
| `[nomeService]` | [regra RN-001] | `[estado]` | `[comportamento esperado]` |

### 11.2 Testes de Integração

<!-- Fluxos específicos de ponta a ponta com dados reais ou de test. -->

| Fluxo | Setup | Ação | Resultado Esperado |
|-------|-------|------|-------------------|
| [Fluxo feliz] | [Estado inicial do DB] | `[MÉTODO] [/path]` com `[payload]` | Status [200] + `[response body]` |
| [Fluxo de erro] | [Estado inicial] | `[request inválido]` | Status [400] + `{ error: "[mensagem]" }` |

### 11.3 Validação Manual

<!-- Passos exatos para validar visualmente ou via interação. -->

1. [Passo 1: navegar para / executar ação X]
2. [Verificar que Y aparece / acontece]
3. [Testar cenário de erro: fazer Z]
4. [Verificar que mensagem W aparece]

---

## 12. Edge Cases

<!-- Situações não óbvias que o código deve tratar. Derivados do PRD seção 11. -->

| Cenário | Condição | Tratamento Esperado |
|---------|----------|---------------------|
| [EC-001] | [O que acontece de incomum] | [Como o código deve reagir] |
| [EC-002] | [O que acontece de incomum] | [Como o código deve reagir] |

---

## 13. Riscos

<!-- Riscos específicos desta task. O que pode dar errado e como mitigar. -->

| Risco | Probabilidade | Impacto | Mitigação |
|-------|--------------|---------|-----------|
| [Risco específico] | Alta/Média/Baixa | Alto/Médio/Baixo | [Ação preventiva] |

---

## 14. Observações para Execução

<!-- Informações adicionais relevantes para o agente ou desenvolvedor que vai executar. -->

- [Convenção de código relevante para esta task]
- [Armadilha conhecida a evitar]
- [Decisão de design que pode parecer errada mas é intencional]
- [Recurso de referência útil]
