# Rastreability Rules — rpi-builder

Convenções de nomes, IDs e rastreabilidade entre task, research e plan.

---

## Convenção de Nomes

O identificador base de uma task é o nome do arquivo sem a extensão:

```
.ai/product/tasks/001-setup-auth.md
                  └─── base ID: 001-setup-auth
```

O mesmo ID base é usado em todos os artefatos derivados:

```
.ai/product/tasks/001-setup-auth.md       ← definição
.ai/product/research/001-setup-auth.md    ← análise
.ai/product/plans/001-setup-auth.md       ← plano
```

**Nunca** criar research ou plan com nome diferente da task correspondente.

---

## Derivação de Paths

Dado o path de uma task:
```
@.ai/product/tasks/001-setup-auth.md
```

Derivar:
```
research: .ai/product/research/001-setup-auth.md
plan:     .ai/product/plans/001-setup-auth.md
```

Dado o path de um plan:
```
@.ai/product/plans/001-setup-auth.md
```

Derivar:
```
task:     .ai/product/tasks/001-setup-auth.md
research: .ai/product/research/001-setup-auth.md
```

---

## Seção de Referências Obrigatória

Todo research.md e plan.md deve começar com uma seção de referências que explicita todos os artefatos relacionados:

```markdown
## 1. Referências

- Task: .ai/product/tasks/001-setup-auth.md
- PRD: .ai/product/prd.md
- Design: .ai/product/design.md
```

Para plan.md, adicionar também:
```markdown
- Research: .ai/product/research/001-setup-auth.md
```

---

## Verificação de Consistência

Ao iniciar qualquer modo, verificar:

1. O ID base extraído do path está correto?
2. Os arquivos derivados existem nos paths esperados?
3. A seção de referências no arquivo aponta para os paths corretos?

Se houver inconsistência de nomes (ex: research com nome diferente da task):
- Avisar explicitamente
- Não assumir que são o mesmo artefato
- Solicitar correção antes de continuar

---

## Numeração e Ordenação

Tasks são processadas em ordem numérica crescente.

O modo `next` e o modo `status` sempre ordenam por prefixo numérico:
```
001 → 002 → 003 → ...
```

Se o prefixo numérico estiver ausente, ordenar alfabeticamente e avisar.

---

## Criação de Pastas

Se `.ai/product/research/` não existir ao gerar um research: criar automaticamente.
Se `.ai/product/plans/` não existir ao gerar um plan: criar automaticamente.
Usar o tool Write para criar o arquivo — o diretório é criado implicitamente.
