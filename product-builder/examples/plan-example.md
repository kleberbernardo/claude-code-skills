# Plan Example — FE-TASK-001: Modal de Nova Tarefa

Exemplo de plano de frontend gerado para o FreelaTask.

---

```markdown
# Plano Frontend — FE-TASK-001: Modal de Nova Tarefa

## 1. Análise do Projeto
- Framework: Next.js 15 App Router, React 19
- Estado: useState (sem Zustand, projeto simples)
- Chamadas API: fetch nativo
- Estilos: Tailwind CSS 4
- Sem biblioteca de formulários (manual com useState)

Padrão identificado: o projeto já tem modal em `ModalNovaTarefa.tsx` mas sem integração de API — só estado local. A task BE-TASK-001 criará o endpoint real.

## 2. Arquivos a Modificar
| Arquivo | Ação | Descrição |
|---------|------|-----------|
| `app/components/ModalNovaTarefa.tsx` | Modificar | Adicionar integração com API |
| `app/page.tsx` | Modificar | Passar handler de submit com fetch |

## 3. Estado Adicional Necessário
- `isSubmitting: boolean` — para loading state
- `apiError: string | null` — para erro de API

## 4. Chamada de API
```typescript
// Ao submeter:
setIsSubmitting(true);
try {
  const res = await fetch('/api/tarefas', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ titulo, cliente, valor: valorNum, status, prioridade })
  });
  if (!res.ok) {
    const data = await res.json();
    setApiError(data.error ?? 'Erro ao criar tarefa');
    return;
  }
  const novaTarefa = await res.json();
  setTarefas(prev => [...prev, deserializarTarefa(novaTarefa)]);
  fecharModal();
} catch {
  setApiError('Não foi possível conectar. Tente novamente.');
} finally {
  setIsSubmitting(false);
}
```

## 5. Critérios de Aceite Técnicos
- Botão "Adicionar" disabled quando isSubmitting
- Texto muda para "Adicionando..." durante submissão
- apiError aparece abaixo do formulário em vermelho
- Modal fecha apenas em caso de sucesso
```
