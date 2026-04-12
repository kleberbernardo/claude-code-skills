# Scope Rules — SDD Builder

Como definir, proteger e comunicar o escopo em todos os modos.

---

## Princípio Central

**Escopo é o que foi explicitamente confirmado pelo usuário.**

Tudo que não foi confirmado é fora do escopo por padrão.

---

## Regras de Inclusão

Uma funcionalidade entra no escopo quando:
- O usuário descreveu ou confirmou explicitamente
- Existe uma regra de negócio que a torna obrigatória
- É pré-requisito técnico direto de algo confirmado

Uma funcionalidade fica fora do escopo quando:
- O usuário não mencionou e não foi confirmada
- Foi mencionada mas marcada como "depois", "v2", "não agora"
- É uma melhoria ou otimização de algo já definido
- É assumida mas não validada

---

## Registrando Premissas

Quando precisar assumir algo não confirmado:
- Marcar com `[ASSUMIDO: descrição da premissa]` no documento
- Adicionar à seção "Premissas" do PRD
- Adicionar à seção "Itens em Aberto" do Design quando relevante

Exemplo:
```
[ASSUMIDO: autenticação via email/senha — usuário não especificou método de auth]
```

---

## Seção "Fora de Escopo"

Toda especificação deve conter uma seção explícita de fora de escopo.

Itens típicos que devem ser avaliados:
- Integração com sistemas externos não mencionados
- Funcionalidades presentes em produtos similares mas não confirmadas
- Variações de fluxo não descritas pelo usuário
- Mobile/Web se apenas um foi confirmado
- Internacionalização (i18n) se não mencionada
- Relatórios e dashboards se não solicitados
- APIs públicas se não mencionadas
- Funcionalidades administrativas internas

---

## Proteção de Escopo no Modo Design

No modo `design`, o escopo é determinado pelo PRD.

- Nunca adicionar módulos, endpoints ou tabelas não derivados do PRD
- Se uma decisão técnica implica algo não no PRD: registrar em "Itens em Aberto"
- Se houver ambiguidade no PRD: registrar em "Itens em Aberto" e assumir a opção mais simples

---

## Proteção de Escopo no Modo Tasks

No modo `tasks`, o escopo é determinado pelo PRD + Design.

- Nunca adicionar tasks de features não presentes no Design
- Nunca expandir o escopo de uma task além do Design
- Se uma task parecer incompleta sem algo do Design: verificar se está no Design
- Se não estiver: registrar como task separada de "configuração" ou "setup"

---

## Checklist de Escopo por Documento

### PRD
- [ ] Seção "Escopo Funcional" lista apenas o que foi confirmado
- [ ] Seção "Fora de Escopo" existe e tem pelo menos 3 itens
- [ ] Premissas estão explicitamente marcadas
- [ ] Dúvidas em Aberto registram incertezas não resolvidas

### Design
- [ ] Todos os módulos têm correspondência com requisitos do PRD
- [ ] Todos os endpoints têm correspondência com fluxos do PRD
- [ ] Não há tabelas/campos sem uso previsto no PRD
- [ ] Itens em Aberto registram decisões que dependem de clarificação

### Tasks
- [ ] Cada task tem referência explícita ao requisito e componente do Design que implementa
- [ ] Nenhuma task implementa algo não presente no Design
- [ ] Fora de escopo de cada task lista o que não deve ser feito
