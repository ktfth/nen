# Skill: Business Logic

**Propósito:** Identificar vulnerabilidades de lógica de negócio e abuso de fluxos de forma defensiva.

---

## Entradas Esperadas

- [ ] Scope definition
- [ ] Documentação de fluxos de negócio
- [ ] Regras de negócio conhecidas
- [ ] Attack surface map

---

## Saídas

- `business_logic_findings.md` - Findings de lógica
- `abuse_cases.md` - Casos de abuso identificados

---

## Procedimento

1. **Entender fluxos** - Mapear processos críticos
2. **Identificar regras** - O que deveria ser validado
3. **Criar abuse cases** - Como burlar regras
4. **Testar race conditions** - Operações concorrentes
5. **Testar limites** - Valores extremos
6. **Verificar estado** - Transições válidas
7. **Documentar findings** - Com impacto de negócio

---

## Categorias de Lógica de Negócio

### Fluxo de Transações
| Teste | Abuse Case |
|-------|------------|
| Compra | Aplicar cupom múltiplas vezes |
| Pagamento | Pagar valor negativo |
| Transferência | Transferir para si mesmo |
| Cancelamento | Cancelar após conclusão |

### Limites e Quotas
| Teste | Abuse Case |
|-------|------------|
| Rate limits | Bypass via múltiplas contas |
| Quantidade | Comprar quantidade negativa |
| Valor mínimo | Ordem abaixo do mínimo |
| Trial | Criar múltiplas contas trial |

### Estado e Transições
| Teste | Abuse Case |
|-------|------------|
| Order status | Pular de "pending" para "delivered" |
| User status | Acessar recursos de conta suspensa |
| Feature flags | Acessar feature desabilitada |

---

## Race Conditions

### Cenários Comuns
```
1. Double spending
   - Duas requisições simultâneas de pagamento
   - Saldo verificado antes de ambas completarem

2. TOCTOU (Time of Check to Time of Use)
   - Verificar permissão
   - [window de vulnerabilidade]
   - Executar ação

3. Race em vouchers
   - Múltiplas redemptions simultâneas
   - Contador não atômico
```

### Teste Seguro
```
Enviar 2-3 requests simultâneas
Verificar se estado final é consistente
NÃO enviar centenas (DoS)
```

---

## Abuse Cases Template

```markdown
## Abuse Case: [Nome]

**Fluxo Afetado:** Checkout
**Regra Esperada:** Cupom aplica uma vez por pedido

### Cenário Normal
1. Adicionar item ao carrinho
2. Aplicar cupom "DESCONTO10"
3. Finalizar compra com 10% off

### Cenário de Abuso
1. Adicionar item ao carrinho
2. Aplicar cupom "DESCONTO10"
3. Enviar request de cupom novamente
4. Desconto aplica múltiplas vezes?

### Impacto
Perda financeira - desconto indevido

### Verificação
- [ ] Backend valida uso único
- [ ] Race condition tratada
- [ ] Log de aplicação de cupom
```

---

## Checklist por Funcionalidade

### E-commerce
- [ ] Preço não pode ser negativo
- [ ] Quantidade tem limite máximo
- [ ] Cupom aplica uma vez
- [ ] Frete calculado corretamente
- [ ] Estoque decrementado atomicamente

### Autenticação/Registro
- [ ] Email verification obrigatório
- [ ] Rate limit em reset password
- [ ] Não enumera usuários válidos
- [ ] Força de senha validada

### Finanças
- [ ] Saldo verificado antes de transação
- [ ] Transferência para si mesmo bloqueada
- [ ] Limites diários enforçados
- [ ] Valores arredondados corretamente

---

## Template de Finding

```markdown
## BL-XXX: [Título]

**Categoria:** Race Condition | State Abuse | Limit Bypass
**Severidade:** High
**Fluxo:** Checkout / Cupom

### Descrição
Cupom de desconto pode ser aplicado múltiplas vezes
enviando requests simultâneas.

### Steps to Reproduce
1. Adicionar item ($100)
2. Enviar 3 requests POST /apply-coupon simultaneamente
3. Desconto aplicado 3x = 30% off

### Impacto
- Perda financeira direta
- Abuso de promoções

### Causa Raiz
Verificação de uso não é atômica com aplicação.

### Recomendação
```python
with transaction.atomic():
    if coupon.is_used_by(user):
        raise AlreadyUsed()
    coupon.mark_used(user)
    order.apply_discount(coupon.value)
```
```

---

## Valores Extremos a Testar

| Campo | Valores de Teste |
|-------|------------------|
| Quantidade | -1, 0, 999999, 2^31 |
| Preço | -0.01, 0, 0.001 |
| Datas | Passado, muito futuro |
| Strings | Empty, muito longa, unicode |
| IDs | 0, negativo, inexistente |

---

## Critérios de Qualidade

- Fluxos críticos analisados
- Abuse cases documentados
- Impacto de negócio quantificado
- Recomendações implementáveis

---

## Token Budget

- **Sugerido:** 4K tokens
- **Máximo:** 8K tokens
- **Reduzir:** Focar em fluxos financeiros, tabelas concisas

---

## Fallbacks

| Situação | Ação |
|----------|------|
| Sem documentação de regras | Inferir do código/UI |
| Fluxo complexo | Dividir em sub-fluxos |
| Sem acesso a ambiente de teste | Análise de código apenas |
| Regras ambíguas | Documentar incerteza |

---

## Red Flags no Código

| Pattern | Risco |
|---------|-------|
| Check sem lock | Race condition |
| Validação só no frontend | Bypass fácil |
| Floating point para dinheiro | Erros de arredondamento |
| Estado em sessão cliente | Manipulação |
