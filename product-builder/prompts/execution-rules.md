# Execution Rules — Product Builder

Regras invioláveis para execução de tasks.

---

## Regra 1: Ler Antes de Escrever

Antes de criar ou modificar qualquer arquivo:
1. Ler o arquivo existente (se houver)
2. Entender o contexto ao redor
3. Identificar importações e dependências
4. Verificar que não vai quebrar código vizinho

---

## Regra 2: Seguir o Plano

O plano aprovado é a lei. Não improvisar.  
Desvios permitidos apenas se:
- O plano contém erro técnico
- O projeto mudou desde o plano
- Desvio melhora sem mudar escopo

Em qualquer caso: alertar o usuário antes de desviar.

---

## Regra 3: Escopo Cirúrgico

Implementar exatamente o que está na task. Nem mais, nem menos.

❌ Proibido:
- Refatorar código vizinho "enquanto está aqui"
- Adicionar feature que "faz sentido adicionar"
- Mudar padrão de código não relacionado
- Corrigir bug encontrado (criar task separada)

✅ Permitido:
- Corrigir erro que o próprio código da task introduziria
- Adicionar importação necessária
- Criar arquivo de tipo compartilhado se necessário

---

## Regra 4: Padrões do Projeto

Nunca introduzir novo padrão sem justificar.  
Se o projeto usa `fetch`, usar `fetch`.  
Se usa `react-hook-form`, usar `react-hook-form`.  
Se usa `zod`, usar `zod`.

---

## Regra 5: Zero Debug Code

Após implementação, verificar:
- Sem `console.log`
- Sem `console.error` de debug (logs de erro real são ok)
- Sem `TODO` não intencionais
- Sem `FIXME` não intencionais
- Sem código comentado

---

## Regra 6: TypeScript Sem `any`

Usar `any` apenas se:
- Tipo genuinamente desconhecido em tempo de compilação
- Integração com biblioteca sem tipos
- Sempre com comentário explicando por quê

Preferir: `unknown` + type guard, `Record<string, unknown>`, generics.

---

## Regra 7: Verificar Build

Após implementar:
```bash
npx tsc --noEmit  # verificar tipos
npm run lint       # verificar lint
```

Não marcar task como done com erros de TypeScript ou lint crítico.

---

## Regra 8: Log de Execução

Criar log em `.ai/product/05-execution/logs/` com:
- Arquivos criados/modificados
- Decisões tomadas em execução
- Desvios do plano (se houver) com motivo
- Pendências para tasks futuras

---

## O que Fazer com Bugs Encontrados

Se encontrar bug não relacionado à task:
1. Documentar no log de execução
2. Não corrigir agora
3. Sugerir criação de task separada
4. Continuar com o escopo da task atual
