# Exemplo — Plano de Correção (modo plan)
<!-- Continuação do Bug 001 — token-expira-cedo. Esta seção é anexada ao mesmo arquivo .ai/debug/001-*.md -->

---

## 5. Plano de Correção
<!-- Gerado por /bug-hunter plan — Data: 2026-06-14 -->

### Abordagem
Alinhar a configuração de staging/produção com o nome novo da env var e tornar a ausência da configuração explícita, em vez de silenciosamente cair em um TTL curto. Correção mínima: corrigir a fonte da divergência (env var ausente) e proteger contra repetição.

Duas frentes:
1. Definir `ACCESS_TOKEN_TTL=86400` nos ambientes (staging e produção).
2. Trocar o fallback silencioso por falha explícita quando a var não estiver definida — para o bug não voltar mascarado.

> Alternativa considerada: apenas mudar o fallback de `900` para `86400`. Descartada como solução principal porque mantém o fallback silencioso, que foi exatamente o que escondeu o problema.

### Pontos de mudança

#### config — `config/auth.ts`

**Trecho atual (antes):** `config/auth.ts:9`
```ts
accessTokenTtl: Number(process.env.ACCESS_TOKEN_TTL) || 900,
```

**Mudança proposta:**
```ts
accessTokenTtl: requireEnvNumber('ACCESS_TOKEN_TTL'),
```

**Por quê:** elimina o fallback silencioso de 900s que é a causa raiz do TTL errado. Com `requireEnvNumber`, a ausência da variável falha no boot, alto e visível, em vez de degradar a sessão para 15 min.

#### util — `src/config/env.ts`

**Trecho atual (antes):** não existe helper de env obrigatória.

**Mudança proposta:** adicionar
```ts
export function requireEnvNumber(name: string): number {
  const raw = process.env[name];
  if (raw === undefined) throw new Error(`Env var obrigatória ausente: ${name}`);
  const n = Number(raw);
  if (Number.isNaN(n)) throw new Error(`Env var ${name} não é numérica: ${raw}`);
  return n;
}
```

**Por quê:** dá um mecanismo reutilizável para falhar alto em configs obrigatórias — diretamente ligado à causa contribuinte (fallback que mascara ausência).

#### ambiente — `.env.staging` / `.env.production`

**Mudança proposta:** adicionar `ACCESS_TOKEN_TTL=86400`.

**Por quê:** restaura o valor correto de 24h sob o nome novo da variável introduzido no commit `a1b2c3d`.

### Não tocar
- `src/auth/token.ts` e `src/auth/middleware.ts` — funcionam corretamente; o defeito é a entrada de config, não a emissão/verificação.

### Impacto e efeitos colaterais
| Quem usa o código tocado | Como é afetado | Risco |
|--------------------------|----------------|-------|
| boot da aplicação | passa a exigir `ACCESS_TOKEN_TTL` definida | médio — exige atualizar todos os ambientes antes do deploy |
| `signAccessToken` | recebe o TTL correto (86400) | baixo |

Mitigação: atualizar todos os `.env` antes de subir; documentar a var como obrigatória.

### Estratégia de teste
- **Teste que falha hoje:** unitário em `signAccessToken` verificando que `exp - iat === 86400` com `ACCESS_TOKEN_TTL=86400` — hoje, sem a var, o config resolve para 900 e o teste falha.
- **Teste que protege depois:** o mesmo teste passa; e um teste de que `requireEnvNumber('ACCESS_TOKEN_TTL')` lança quando a var está ausente.
- **Tipo:** unitário (jest, padrão do projeto).
- **Casos de borda a cobrir:** var ausente (lança), var não numérica (lança), var válida (retorna número).
- **Arquivo de teste:** `tests/config/env.test.ts` e `tests/auth/token.test.ts`.

### Critérios de conclusão
- [ ] Token emitido tem `exp - iat === 86400`
- [ ] App falha no boot se `ACCESS_TOKEN_TTL` estiver ausente
- [ ] Testes novos passam; suíte de auth sem regressão
