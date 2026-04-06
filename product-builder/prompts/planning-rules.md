# Planning Rules — Product Builder

Regras para gerar planos técnicos de qualidade.

---

## Regra 1: Explorar Antes de Planejar

Nunca gerar plano sem explorar o projeto real.  
Plano baseado em suposições → retrabalho.

Exploração mínima:
- Stack e versões
- Estrutura de arquivos
- Padrões de código existentes
- Componentes/utils reutilizáveis

---

## Regra 2: Plano Específico, Não Genérico

❌ Genérico: "Criar componente de formulário com validação"  
✅ Específico: "Criar `components/TarefaForm.tsx` com campos titulo (min 3), cliente, valor (> 0), usando useState para estado local e fetch para POST /api/tarefas"

Cada arquivo deve ser nomeado com path real.  
Cada decisão deve ter motivo.

---

## Regra 3: Planos de Frontend e Backend São Independentes

Um plano de FE-TASK não pode dizer "implementar o backend".  
Um plano de BE-TASK não pode dizer "criar a tela".

Cada plano é autocontido para sua camada.

---

## Regra 4: Identificar Reutilização

Antes de planejar criar X, verificar se X já existe:
```bash
grep -rn "NomeComponente\|nomeFunction\|interfaceNome" app/ lib/
```

Se existir e puder ser reutilizado: usar.  
Se existir mas precisar de modificação: planejar a modificação.

---

## Regra 5: Decisões Técnicas Documentadas

Toda escolha não óbvia deve ter justificativa:

| Decisão | Alternativa | Motivo |
|---------|------------|--------|
| useState local | Zustand | Estado não precisa ser compartilhado |
| fetch nativo | axios | Já é padrão no projeto |
| Zod schema | validação manual | Consistência com outras rotas |

---

## Regra 6: Critérios de Aceite Técnicos

O plano deve ter ACs técnicos (não apenas funcionais):

```
Given POST /api/tarefas com body válido
When executado com token válido
Then retorna 201 com { id, titulo, cliente, valor, status, createdAt }
```

Esses ACs são usados pelo QA para validar a implementação.

---

## Regra 7: Alertar Sobre Riscos

Se o plano encontrar riscos:
- Schema de banco que pode quebrar dados existentes
- Breaking change em API
- Performance concern com N+1
- Task dependency não satisfeita

Sempre alertar antes de finalizar o plano.
