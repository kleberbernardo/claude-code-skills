# PRD Questions — Product Builder

Perguntas para aprofundar features durante o PRD. Usar uma por vez.

---

## Regras de Negócio

- "Quem pode [fazer ação X]? Só o criador? Qualquer usuário? Apenas admins?"
- "O que acontece se [usuário A] tentar [ação] no item de [usuário B]?"
- "Tem algum limite numérico? (máximo de itens, valor mínimo, prazo de expiração)"
- "Essa ação é reversível? Tem confirmação ou pode desfazer?"
- "O que acontece com os dados relacionados quando [item principal] é deletado?"

---

## Fluxo do Usuário

- "Me conta o passo a passo do início ao fim — sem pular etapas."
- "E se o usuário [sair no meio do fluxo]? O progresso é salvo?"
- "Existe notificação ou confirmação após [ação X]?"
- "Esse fluxo muda dependendo de quem está logado?"

---

## Estados de UI

- "O que o usuário vê enquanto [operação X] está acontecendo?"
- "E se a lista vier vazia? O usuário vê o quê — tela em branco ou algo específico?"
- "Quando dá erro, o usuário precisa de alguma orientação além da mensagem?"
- "Tem algum estado 'parcialmente carregado' (ex: lista carrega mas fotos ainda não)?"

---

## Validações

- "O campo [X] aceita qualquer texto ou tem formato específico?"
- "Qual é o comprimento máximo de [campo]?"
- "A validação aparece enquanto o usuário digita ou só quando ele tenta salvar?"
- "Existe validação cruzada entre campos? (ex: data fim > data início)"

---

## Permissões

- "Quem vê o quê? Existe informação que só o dono pode ver?"
- "Existe algum campo que só admin pode editar?"
- "Convites ou compartilhamento fazem parte desta feature?"

---

## Integrações

- "Esta feature depende de dados externos (API, serviço terceiro)?"
- "Precisa de email de confirmação ou notificação para esta ação?"
- "Existe webhook ou evento que deve ser disparado?"
