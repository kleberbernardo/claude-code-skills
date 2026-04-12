# Plan - Task [NNN]
<!-- Gerado por /rpi-builder plan -->
<!-- Data: YYYY-MM-DD -->

---

## 1. Referências

- Task: .ai/product/tasks/[NNN-task-name].md
- Research: .ai/product/research/[NNN-task-name].md
- PRD: .ai/product/prd.md
- Design: .ai/product/design.md

---

## 2. Objetivo da Implementação

<!-- O que será entregue ao final da implementação. Uma frase clara e verificável. -->

Implementar [o quê] de forma que [critério de conclusão principal].

---

## 3. Estratégia Geral

<!-- Como será feito e por quê. 3–5 linhas. Descrever a abordagem, não a lista de passos. -->

[Ex: Seguir o padrão handler → service → repository já estabelecido no projeto. O handler gerencia parsing e autenticação, o service encapsula a lógica de negócio e as validações de regras de negócio, e o acesso ao banco é feito diretamente via Drizzle no service. Essa estrutura mantém consistência com os módulos auth e users existentes.]

---

## 4. Escopo Exato da Implementação

<!-- O que esta implementação FAZ. Específico e completo. -->

Esta implementação inclui:
- [ ] [Componente/ação 1 — específico]
- [ ] [Componente/ação 2 — específico]
- [ ] [Componente/ação 3 — específico]

---

## 5. Fora de Escopo

<!-- O que explicitamente NÃO deve ser feito. Protege o escopo contra expansão durante a implementação. -->

- [Item 1 que parece relacionado mas não é desta task]
- [Refatoração X que seria natural mas não foi solicitada]
- [Feature adjacente que pertence à task NNN]
- Nenhuma alteração em arquivos fora da lista da seção 6 e 7

---

## 6. Arquivos a Criar

<!-- Paths completos. Para cada arquivo: o que conterá. -->

| Arquivo | Descrição do conteúdo |
|---------|----------------------|
| `src/[caminho]/[arquivo].[ext]` | [O que este arquivo vai conter] |

---

## 7. Arquivos a Modificar

<!-- Paths completos. Para cada arquivo: exatamente o que muda — não o arquivo inteiro. -->

| Arquivo | O que muda |
|---------|-----------|
| `src/[caminho]/[arquivo].[ext]` | [Descrição específica do que será adicionado/alterado] |

---

## 8. Componentes Impactados

<!-- Outros módulos ou partes do sistema que são afetados pela implementação. -->

- **[Módulo X]:** [Como é afetado — ex: passa a importar o novo service]
- **[Módulo Y]:** [Como é afetado — ex: schema do banco recebe nova tabela]

---

## 9. Contratos e Interfaces Envolvidos

<!-- Copiar literalmente do Design Técnico. Não resumir. -->

```typescript
// Copiado de design.md — Seção [N]

type [NomeTipo] = {
  [campo]: [tipo];  // [descrição]
}

// Endpoint
// [MÉTODO] [/path]
// Request: { [campo]: [tipo] }
// Response 2XX: { [campo]: [tipo] }
// Erros: [código] → [condição] → { error: "[mensagem]" }
```

---

## 10. Regras de Negócio a Respeitar

<!-- Copiadas do PRD. Cada regra com condição e resultado. -->

| ID | Regra | Condição | Resultado |
|----|-------|----------|-----------|
| RN-[ID] | [Nome] | [Quando X] | [Então Y] |

---

## 11. Passo a Passo Detalhado da Implementação

<!-- Cada passo é acionável. O implementador não precisa tomar nenhuma decisão. -->

### Pré-condições
- [ ] Verificar que [dependência] existe e está funcionando
- [ ] Confirmar que schema da task [NNN] está aplicado ao banco

### Passos

**1. [Nome do passo — arquivo a criar/modificar]**

Criar/Modificar `src/[caminho]/[arquivo].[ext]`:

```
[Descrição precisa do que implementar neste passo]
- Ponto específico 1
- Ponto específico 2
- Condição ou regra: se X, então Y
```

**2. [Nome do passo]**

Modificar `src/[caminho]/[arquivo].[ext]`:

```
[Descrição precisa do que implementar neste passo]
```

**3. [Nome do passo — testes]**

Criar `tests/[caminho]/[arquivo].test.[ext]`:

```
[Descrição dos testes a implementar neste passo]
```

---

## 12. Estratégia de Testes

### 12.1 Testes Unitários

| Função/Classe | Caso de Teste | Input | Resultado Esperado |
|--------------|--------------|-------|-------------------|
| `[nomeFuncao]` | Happy path | `[input]` | `[output]` |
| `[nomeFuncao]` | [cenário de erro] | `[input inválido]` | `throw [TipoErro]('[mensagem]')` |
| `[nomeFuncao]` | [edge case] | `[input extremo]` | `[comportamento esperado]` |

### 12.2 Testes de Integração

| Fluxo | Setup | Request/Ação | Resultado Esperado |
|-------|-------|-------------|-------------------|
| Happy path | [Estado inicial do DB/sistema] | `[MÉTODO] [/path]` com `[payload]` | Status `[código]` + `[response body]` |
| [Cenário de erro] | [Estado] | `[request/ação inválida]` | Status `[código]` + `{ error: "[mensagem]" }` |
| [Edge case] | [Estado] | `[request/ação]` | `[resultado esperado]` |

### 12.3 Validação Manual

1. [Passo 1: ação concreta para testar]
2. [Passo 2: verificar que resultado X ocorre]
3. [Passo 3: testar cenário de erro Y]
4. [Passo 4: verificar mensagem de erro Z]

---

## 13. Critérios de Conclusão

<!-- Verificáveis. Cada item é sim/não. Implementação só está pronta quando todos são verdadeiros. -->

- [ ] [Funcionalidade X] funciona conforme fluxo do PRD/Design
- [ ] Regra RN-[ID] é aplicada e testada
- [ ] [Endpoint/função] retorna [código/valor] para [condição de erro]
- [ ] Todos os testes da seção 12 passam sem erros
- [ ] Sem erros de TypeScript / lint nos arquivos criados/modificados
- [ ] [Validação manual: ação específica produz resultado esperado]

---

## 14. Riscos

<!-- Riscos identificados durante o research que o implementador deve ter em mente. -->

| Risco | Origem | Mitigação |
|-------|--------|-----------|
| [Risco do research] | `src/[arquivo]:linha` | [Como evitar] |

---

## 15. Itens que Não Devem Ser Alterados

<!-- Lista explícita para evitar scope creep durante a implementação. -->

- `[arquivo/módulo]` — [por que não deve ser tocado]
- Não refatorar [função/classe X] mesmo que pareça necessário
- Não alterar testes existentes em `tests/[...]`
- Não modificar schema de tabelas além do especificado

---

## 16. Observações Finais para Execução

<!-- Informações adicionais que o implementador precisa saber. -->

- [Convenção de código específica deste projeto a seguir]
- [Armadilha do research a evitar]
- [Decisão que foi tomada conscientemente e pode parecer errada]
- [Ponto de atenção sobre o ambiente de desenvolvimento]
