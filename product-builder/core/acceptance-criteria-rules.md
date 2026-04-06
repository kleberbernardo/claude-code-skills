# Acceptance Criteria Rules — Product Builder

Como escrever critérios de aceite que realmente funcionam.

---

## O que São Critérios de Aceite

Critérios de aceite (AC) são as condições que, quando todas satisfeitas, provam que uma feature ou task está completa e funcionando corretamente. São a ponte entre produto e engenharia.

**Bom AC:**
- Testável: qualquer pessoa pode verificar sem ambiguidade
- Específico: sem "deve funcionar bem" ou "deve ser rápido"
- Completo: cobre happy path, edge cases e erros
- Independente: não depende de interpretação subjetiva

---

## Formato Padrão: Given/When/Then

```
AC-001: [Título descritivo]
Given [contexto/estado inicial]
When [ação do usuário ou evento]
Then [resultado esperado]
```

### Exemplos Bons
```
AC-001: Login com credenciais válidas
Given o usuário está na tela de login
When digita email e senha corretos e clica em "Entrar"
Then é redirecionado para o dashboard e vê seu nome no header

AC-002: Login com senha incorreta
Given o usuário está na tela de login
When digita email correto mas senha errada e clica em "Entrar"
Then vê mensagem "E-mail ou senha incorretos" e o campo senha é limpo

AC-003: Login após 5 tentativas falhas
Given o usuário errou a senha 4 vezes
When tenta pela 5ª vez com senha incorreta
Then a conta é bloqueada por 15 minutos e a mensagem explica o motivo
```

### Exemplos Ruins
```
❌ AC: O login deve funcionar corretamente
❌ AC: A senha deve ser validada
❌ AC: O usuário vê o dashboard
```

---

## Categorias Obrigatórias por Feature

Para cada feature, os ACs devem cobrir:

### 1. Happy Path
O fluxo principal quando tudo está correto.
```
AC-001: [Ação principal sucede com dados válidos]
```

### 2. Validação de Input
Cada campo com regra deve ter AC de erro.
```
AC-002: Campo obrigatório vazio mostra mensagem específica
AC-003: Formato inválido mostra mensagem específica
AC-004: Valor fora do range mostra mensagem com o limite
```

### 3. Estados de Loading
```
AC-005: Durante submit, botão mostra estado de loading e fica desabilitado
AC-006: Skeleton/spinner aparece durante carregamento de dados
```

### 4. Estados de Erro
```
AC-007: Erro de rede mostra mensagem genérica e botão "Tentar novamente"
AC-008: Erro do servidor (500) mostra mensagem de suporte
AC-009: Recurso não encontrado (404) redireciona para página correta
```

### 5. Estado Vazio
```
AC-010: Lista sem itens mostra empty state com CTA para criar o primeiro
```

### 6. Permissões/Autorização
```
AC-011: Usuário sem permissão vê 403 ou é redirecionado, não vê dados
AC-012: Usuário não autenticado é redirecionado para login com redirect_uri
```

### 7. Responsividade (Frontend)
```
AC-013: Em mobile (375px), layout é legível e interativo
AC-014: Em tablet (768px), layout não quebra
```

---

## AC Técnicos (para tasks de plano)

Para tasks de backend, ACs técnicos são válidos:

```
AC-T001: Endpoint retorna 200 com schema correto para input válido
AC-T002: Endpoint retorna 400 com mensagem de erro para input inválido
AC-T003: Endpoint retorna 401 se token ausente/expirado
AC-T004: Endpoint retorna 403 se usuário não tem permissão
AC-T005: Query não executa N+1 para lista de 100 itens
AC-T006: Operação é idempotente (re-executar não duplica)
```

---

## Níveis de Prioridade de AC

| Prioridade | Descrição | Quando bloqueante? |
|-----------|-----------|-------------------|
| P0 | Crítico — impede uso | Sempre |
| P1 | Importante — degradação significativa | Sim |
| P2 | Nice-to-have — melhoria | Não para MVP |

Marcar cada AC com prioridade:
```
[P0] AC-001: Login com dados válidos funciona
[P1] AC-002: Timeout de sessão redireciona para login
[P2] AC-003: Animação de transição entre telas
```

---

## Como o QA usa os ACs

O modo QA lê os ACs e gera um relatório com:
- ✅ Passou: verificado e funcionando
- ❌ Falhou: testado e não funciona
- ⚠️ Parcial: funciona em alguns cenários
- 🔲 Não testado: não foi possível verificar

Se qualquer AC P0 falhar → QA BLOQUEADO → não pode marcar feature como done.

---

## Anti-patterns a Evitar

| Anti-pattern | Por quê é ruim | Alternativa |
|-------------|----------------|-------------|
| "Funciona corretamente" | Não testável | Especificar o resultado exato |
| "Deve ser rápido" | Sem métrica | "Responde em < 500ms com 100 itens" |
| "Parece bonito" | Subjetivo | Especificar o estado visual |
| "Não trava" | Vago | Especificar o comportamento de erro |
| "Integra com X" | Incompleto | "Chama API X e persiste response em Y" |
