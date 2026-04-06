# QA Rules — Product Builder

Regras para conduzir QA efetivo.

---

## Regra 1: Fonte de Verdade São os ACs

QA verifica os Critérios de Aceite — não o que acha que deveria funcionar.  
Se um AC não foi escrito, não pode reprovar por ele.  
Se encontrar problema não coberto por AC → registrar como sugestão (P2), não bloqueante.

---

## Regra 2: Separar Confirmado de Suspeita

Ao reportar:
- **Confirmado:** evidência no código que comprova o problema
- **Suspeita:** parece errado mas não conseguiu confirmar por análise estática

Não reportar suspeita como bloqueante. Testar ou investigar mais.

---

## Regra 3: Prioridade Correta

| Prioridade | Quando usar |
|-----------|------------|
| P0 Bloqueante | AC P0 falhou; feature não funciona no happy path |
| P1 Importante | AC P1 falhou; experiência degradada mas feature usável |
| P2 Melhoria | Não estava no AC; seria bom ter |

Não inflar prioridades. Um P2 chamado de P0 perde credibilidade.

---

## Regra 4: Evidência Obrigatória

Para cada gap reportado:
- Citar o arquivo e linha onde o problema está
- Mostrar o código ou comportamento problemático
- Explicar o que deveria ser diferente

❌ "O loading não está implementado"  
✅ "O componente `TaskList` (app/components/TaskList.tsx:23) não tem estado de loading — ao montar, renderiza lista vazia por ~200ms antes dos dados chegarem."

---

## Regra 5: Bloqueante P0 = Feature Não Done

Se qualquer gap P0 existir, a feature NÃO pode ser marcada como done.  
O resultado do QA é FALHOU ou PARCIAL.

Apenas quando todos os P0 forem corrigidos e re-verificados → PASSOU.

---

## Regra 6: Re-QA Foca nos Gaps

Ao re-executar QA após correções:
1. Verificar apenas os gaps que foram reportados
2. Confirmar que foram corrigidos
3. Verificar que a correção não introduziu novos problemas
4. Não re-verificar ACs que já passaram
