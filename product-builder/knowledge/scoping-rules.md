# Scoping Rules — Product Builder

Como definir o escopo de features, tasks e execução.

---

## Regra de Ouro

**Fazer menos, mas fazer direito.**

Uma feature bem implementada > cinco features pela metade.  
Uma task com estados de loading/error > dez tasks que travam silenciosamente.

---

## Scoping de Features

### Feature está no MVP se:
- Sem ela, o produto não resolve o problema central
- O usuário não consegue completar a jornada principal
- Gera receita ou é pré-requisito direto para gerar receita

### Feature fica para v2 se:
- É melhorias de UX (dark mode, animações, atalhos de teclado)
- É analytics e relatórios
- É funcionalidade de nichos (apenas ~5-10% dos usuários usariam)
- É integrações avançadas (já existe alternativa manual)
- É notificações complexas (email simples pode ser suficiente)

---

## Scoping de Tasks

### Uma task deve ser concluída em 1-2 dias por uma pessoa

Se estiver levando mais:
- Muito grande → dividir
- Muito complexa → re-planejar antes de continuar
- Dependências não satisfeitas → resolver antes

### Critérios para dividir uma task

Dividir quando:
- Tem 2+ endpoints independentes
- Tem 3+ telas diferentes
- Tem lógica de banco E lógica de negócio complexas juntas
- QA levaria mais de meio dia separadamente

### Critérios para NÃO dividir

Não dividir quando:
- A task toda leva < 2 horas
- Dividir criaria dependência artificial
- O teste de uma parte requer a outra funcionando

---

## Scoping da Execução

### O que está no escopo:
- Tudo que está no plano da task
- Código necessário para o plano funcionar (importações, tipos)
- Testes para os ACs da task (se projeto tiver testes)

### O que está fora do escopo:
- Melhorias que surgem durante execução
- Bugs em código adjacente (registrar, não corrigir)
- Features que "fazem sentido adicionar"
- Refatoração de código não relacionado

---

## Como Lidar com "Creep" de Escopo

Durante execução, se perceber que algo útil poderia ser adicionado:

1. **Parar** — não implementar ainda
2. **Registrar** no log de execução: "Identificado oportunidade: [descrição]"
3. **Continuar** com o escopo original
4. **Após concluir** a task atual, criar nova task para a melhoria

Por quê: scope creep é a causa #1 de tasks "quase prontas" que nunca terminam.
