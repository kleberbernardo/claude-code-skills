# Frontend Best Practices — Product Builder

Práticas essenciais para frontend de produção.

---

## Sempre Implementar os 4 Estados

Toda tela com dados assíncronos deve ter:
1. **Loading** — skeleton ou spinner, nunca tela em branco
2. **Success** — dados renderizados com tipagem
3. **Empty** — empty state com CTA, nunca lista vazia sem contexto
4. **Error** — mensagem + ação de retry

---

## Feedback Imediato

Para toda ação do usuário, dar feedback em < 200ms:
- Clicou em botão → botão muda de estado imediatamente
- Submeteu formulário → loading state aparece imediatamente
- Ação concluiu → toast/mensagem de confirmação
- Erro ocorreu → mensagem clara + o que fazer

---

## Formulários

- Validar no blur (quando sai do campo) + no submit
- Botão submit desabilitado durante submissão
- Limpar formulário após sucesso (não após erro)
- Foco automático no primeiro campo de formulários modais
- Enter no último campo submete o formulário

---

## Responsividade

- Mobile first: estilizar para < 640px, depois adicionar para telas maiores
- Testar em 375px (iPhone SE), 768px (iPad), 1280px (desktop)
- Nenhum elemento deve ter overflow horizontal em mobile
- Touch targets mínimo 44×44px
- Texto legível sem zoom em mobile

---

## Performance

- Usar `loading.tsx` no Next.js para streaming
- Imagens sempre com `next/image` (quando Next.js)
- Não fazer requests desnecessários ao montar componente
- Cancelar requests pendentes quando componente desmonta
- Listas longas (> 100 itens) → virtualização

---

## Acessibilidade Básica

- Botões e links com texto descritivo (não "clique aqui")
- Imagens com `alt` text
- Formulários com `label` associado a cada `input`
- Foco visível (não remover outline)
- Estados de loading anunciados para screen readers (`aria-busy`)

---

## Anti-patterns a Evitar

| Anti-pattern | Por quê é ruim |
|-------------|----------------|
| `useEffect` para buscar dados sem cleanup | Memory leak e race conditions |
| Estado derivado em `useState` | Usar `useMemo` ou calcular no render |
| `any` no TypeScript | Bugs silenciosos |
| Sem `key` em listas | Reconciliação incorreta do React |
| Mutação direta de estado | Estado não atualiza, bugs difíceis |
| `document.querySelector` em React | Usar `useRef` |
| CSS inline para tudo | Difícil manter, sem responsividade |
