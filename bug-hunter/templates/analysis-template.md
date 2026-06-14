# Bug [NNN] — [slug]
<!-- Gerado por /bug-hunter analyze -->
<!-- Data: YYYY-MM-DD -->

Status: investigating
RootCause: unknown
Plan: not-started
Fix: not-started

---

## 1. Compreensão

### Comportamento observado
<!-- O que acontece de errado, em termos concretos. -->

### Comportamento esperado
<!-- O que deveria acontecer. -->

**Fonte do comportamento correto:** <!-- spec / doc / URL / regra de negócio / relato do usuário -->
<!-- Se houver URL, registrar e indicar que foi lida via WebFetch. -->

### Reprodução
<!-- Passos exatos para disparar o bug. -->
1.
2.
3.

**Entrada usada:** <!-- dados, usuário, ambiente -->
**Frequência:** <!-- sempre / intermitente / só em produção -->

### Evidência
<!-- Stack trace, mensagem de erro, log, print, valores observados vs esperados. -->

```
[stack trace ou erro literal]
```

### Contexto
- **Branch/versão:** 
- **Funcionava antes?** <!-- se sim, o que mudou: commit/deploy/dependência -->

---

## 2. Análise da Causa Raiz

### Reprodução no código
<!-- Como o gatilho percorre o código até o ponto de falha. -->

### Passo a passo (gatilho → divergência)
<!-- Numerado, do disparo até a linha exata onde o estado/valor passa a estar errado. Cada passo cita arquivo:linha. -->

1. `arquivo:linha` — [o que acontece aqui]
2. `arquivo:linha` — [estado ainda correto]
3. `arquivo:linha` — **ponto de divergência**: [por que o valor/estado fica errado aqui]
4. `arquivo:linha` — [como o erro se propaga até o sintoma observado]

### Causa raiz
<!-- A origem real, em 1–3 frases. Distinta do sintoma. -->

**Localização:** `arquivo:linha`

### Por que acontece
<!-- A explicação causal detalhada, ancorada em evidência. -->

### Causas contribuintes (se houver)
<!-- Condições que tornam o bug possível ou pioram, mas não são a origem. -->

### Hipóteses descartadas
<!-- Pelo menos uma alternativa considerada e por que NÃO é a causa. -->
- ❌ [Hipótese X] — descartada porque [evidência em arquivo:linha]

### Origem (quando "funcionava antes")
<!-- git blame/log do trecho: qual mudança introduziu o defeito. -->

---

## 3. Lacunas e Suposições
<!-- O que não foi possível confirmar. Marcar suposições com [ASSUMIDO: ...]. Se não houver, declarar "Nenhuma". -->

---

## 4. Observações fora de escopo
<!-- Outros problemas notados durante a análise, para não perder — mas que NÃO serão tratados neste bug. -->

<!--
As seções abaixo são preenchidas pelos modos seguintes:
- "Plano de Correção" → /bug-hunter plan
- "Correção Aplicada" → /bug-hunter fix
-->
