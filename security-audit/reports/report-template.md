# Template de Relatório de Auditoria de Segurança

Use este template para gerar o relatório final de toda auditoria.

---

```markdown
# 🔒 SECURITY AUDIT REPORT
**Projeto:** [Nome do Projeto]  
**Data:** [Data da Auditoria]  
**Auditor:** Security Audit Skill v1.0  
**Escopo:** [Modo: deep/quick/auth/secrets/infra/data]  
**Cobertura:** [% de arquivos analisados / áreas cobertas]

---

## 📊 Executive Summary

[2-4 parágrafos descrevendo o estado geral de segurança do projeto, os riscos mais significativos encontrados e o impacto potencial para o negócio. Evitar linguagem técnica excessiva — escrito para liderança.]

### Postura de Segurança Geral
[Escolher: Crítica / Precária / Razoável / Boa / Excelente]

### Top 5 Riscos Identificados
1. [Risco mais crítico — breve descrição]
2. [Segundo risco]
3. [Terceiro risco]
4. [Quarto risco]
5. [Quinto risco]

### Métricas
| Severidade | Quantidade |
|-----------|-----------|
| 🔴 Critical | N |
| 🟠 High | N |
| 🟡 Medium | N |
| 🔵 Low | N |
| ⚪ Info | N |
| **Total** | **N** |

---

## 🔴 CRITICAL ISSUES

> Issues críticos exigem remediação IMEDIATA — em horas/dias, não semanas.

---

### [CRIT-001] [Título Descritivo do Issue]

| Campo | Valor |
|-------|-------|
| **Severidade** | Critical |
| **Confiança** | High / Medium / Low |
| **CVSS Score** | [0.0-10.0] |
| **CWE** | CWE-XXX |
| **OWASP** | AXX:2021 |
| **Affected Files** | `path/to/file.ts:linha` |

**Descrição**  
[Explicação clara do que é a vulnerabilidade e por que é um problema.]

**Evidência**
```
Arquivo: src/routes/auth.ts:47
Trecho: jwt.verify(token, secret)  // algoritmo não especificado
```

**Cenário de Exploração**
[Como um atacante exploraria essa vulnerabilidade, passo a passo. Ser específico sobre o impacto real.]

**Impacto de Negócio**
[O que acontece se explorado: perda de dados, acesso não autorizado, perda financeira, etc.]

**Recomendação de Correção**
```typescript
// Antes (vulnerável)
jwt.verify(token, secret)

// Depois (seguro)
jwt.verify(token, secret, { algorithms: ['HS256'] })
```

**Prioridade:** IMEDIATA — corrigir antes do próximo deploy em produção

---

## 🟠 HIGH ISSUES

> Issues de alta severidade devem ser remediados dentro de 1-2 semanas.

---

### [HIGH-001] [Título]

[Mesmo formato do Critical]

---

## 🟡 MEDIUM ISSUES

> Issues médios devem ser incluídos no próximo sprint de segurança.

---

### [MED-001] [Título]

[Mesmo formato]

---

## 🔵 LOW ISSUES

> Issues baixos podem ser tratados em backlog com prioridade normal.

---

### [LOW-001] [Título]

[Mesmo formato]

---

## ⚪ INFORMATIONAL

> Sugestões de melhoria sem risco de segurança imediato.

- [Item informacional 1]
- [Item informacional 2]

---

## 🗺️ Remediation Roadmap

### Imediato (0-48h)
- [ ] [CRIT-001] Corrigir JWT algorithm confusion
- [ ] [CRIT-002] Rotacionar secret exposta no .env commitado

### Curto Prazo (1-2 semanas)
- [ ] [HIGH-001] Adicionar rate limiting no endpoint de login
- [ ] [HIGH-002] Implementar ownership check nas rotas de recursos

### Médio Prazo (1-4 semanas)
- [ ] [MED-001] Configurar CSP headers
- [ ] [MED-002] Mover secrets para Secrets Manager

### Longo Prazo (backlog)
- [ ] [LOW-001] Atualizar dependências com CVEs menores
- [ ] [LOW-002] Adicionar HSTS preloading

---

## 📋 Cobertura da Auditoria

| Área | Status | Observações |
|------|--------|-------------|
| Secrets/Credenciais | ✅ Verificado | |
| Autenticação | ✅ Verificado | |
| Autorização | ✅ Verificado | |
| Injeção (SQL/Command) | ✅ Verificado | |
| XSS | ✅ Verificado | |
| Configuração de API | ✅ Verificado | |
| Infraestrutura | ⚠️ Parcial | Sem acesso ao ambiente de prod |
| CI/CD | ✅ Verificado | |
| Dependências | ✅ Verificado | |
| Banco de Dados | ✅ Verificado | |

---

## ⚠️ Disclaimer

Esta auditoria foi baseada em análise estática do código-fonte disponível. Não substitui pentest dinâmico, teste em ambiente de produção, ou análise de configuração de infraestrutura ao vivo. Recomendamos complementar com ferramentas de DAST e pentest periódico.

**Secrets encontrados:** Nunca expostos neste relatório — apenas indicação de localização e recomendação de rotação.
```
