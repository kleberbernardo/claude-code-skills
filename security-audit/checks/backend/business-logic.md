# Lógica de Negócio — Vulnerabilidades

Falhas na lógica de negócio são frequentemente ignoradas por scanners automáticos mas causam fraudes reais.

---

## Tipos de Vulnerabilidades de Lógica de Negócio

### 1. Race Conditions (TOCTOU)
**Severidade: High em sistemas financeiros**

```bash
grep -rn "findOne.*then.*update\|findById.*then.*save\|check.*balance.*then.*deduct" --include="*.{js,ts,py}" -n
```

**Exemplo:**
```javascript
// VULNERÁVEL — Time Of Check vs Time Of Use
async function transferMoney(userId, amount) {
  const user = await db.users.findById(userId)
  if (user.balance < amount) throw new Error('Insufficient funds')
  // Atacante faz 100 requests simultâneos aqui
  await db.users.update(userId, { balance: user.balance - amount })
  // Todos os 100 requests passam pela verificação com balance original
}

// SEGURO — usar transação atômica
async function transferMoney(userId, amount) {
  await db.transaction(async (trx) => {
    const result = await trx.raw(
      'UPDATE users SET balance = balance - ? WHERE id = ? AND balance >= ? RETURNING balance',
      [amount, userId, amount]
    )
    if (result.rows.length === 0) throw new Error('Insufficient funds')
  })
}
```

---

### 2. Price Manipulation
**Severidade: Critical em e-commerce**

```bash
grep -rn "req\.body\.price\|req\.body\.amount\|req\.body\.total" --include="*.{js,ts,py}" -n | grep -v "validate\|Stripe\|checkout"
```

**Exemplo:**
```javascript
// VULNERÁVEL — preço vem do cliente
app.post('/api/checkout', async (req, res) => {
  const { productId, price, quantity } = req.body  // NUNCA confiar no preço do cliente
  await processPayment(price * quantity)
})

// SEGURO — preço vem do servidor
app.post('/api/checkout', async (req, res) => {
  const { productId, quantity } = req.body
  const product = await db.products.findById(productId)
  const total = product.price * quantity  // preço do banco, não do cliente
  await processPayment(total)
})
```

---

### 3. Quantidade Negativa
**Severidade: High**

```javascript
// VULNERÁVEL — quantidade negativa em transferência/compra
{ "amount": -100 }  // "transfere" -100 = recebe 100

// VERIFICAR
grep -rn "amount\|quantity\|count" --include="*.{js,ts}" | grep -v "positive\|min.*0\|> 0\|>= 0\|Math\.abs"
```

---

### 4. Coupon Abuse
**Severidade: Medium**

```bash
grep -rn "coupon\|discount\|promo.*code\|voucher" --include="*.{js,ts,py}" -n | grep -v "used\|redeemed\|applied\|count"
```

**Verificar:**
- Cupom pode ser usado mais de uma vez?
- Cupom de um usuário pode ser usado por outro?
- Múltiplos cupons aplicáveis ao mesmo tempo?

---

### 5. Fluxo Manipulável

```bash
# Verificar se há verificação de estado antes de transições
grep -rn "order\.status\|payment\.status\|subscription\.status" --include="*.{js,ts,py}" | grep -v "check\|verify\|validate\|assert\|guard\|if"
```

**Exemplo:** Usuário pula etapa de pagamento e vai direto para confirmação de pedido.

---

## Checklist

- [ ] Operações financeiras usam transações atômicas do banco
- [ ] Preços/valores não são aceitos do cliente
- [ ] Quantidades negativas são rejeitadas
- [ ] Cupons têm controle de uso único e por usuário
- [ ] Transições de estado são validadas (não pode ir de A para C sem B)
- [ ] Limites de negócio são verificados server-side

---

## Red Flags

1. `req.body.price` ou `req.body.amount` em endpoints de pagamento
2. Sem transação de banco em operações de transferência/débito
3. Sem validação de quantidade mínima (> 0)
4. Cupom sem controle de uso
5. Status de pedido pode ser alterado diretamente via API sem validação de fluxo
