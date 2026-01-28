# Skill: AuthZ/AuthN Checks

**Propósito:** Verificar autenticação, autorização, sessões, IDOR e controle de acesso.

---

## Entradas Esperadas

- [ ] Scope definition
- [ ] Attack surface map
- [ ] Roles/perfis do sistema
- [ ] Endpoints autenticados listados

---

## Saídas

- `authz_findings.md` - Findings de controle de acesso
- `access_matrix.md` - Matriz de acesso verificada

---

## Procedimento

1. **Mapear roles** - Identificar perfis e privilégios
2. **Criar matriz de acesso** - Quem pode fazer o quê
3. **Testar autenticação** - Bypass, força bruta, session
4. **Testar autorização** - Vertical e horizontal
5. **Testar IDOR** - Acesso a recursos de outros
6. **Verificar session** - Fixation, hijacking, timeout
7. **Documentar findings** - Por categoria

---

## Matriz de Acesso Template

```markdown
| Recurso/Ação | Anon | User | Admin | Owner |
|--------------|------|------|-------|-------|
| GET /profile | ✗ | ✓ own | ✓ all | - |
| PUT /profile | ✗ | ✓ own | ✓ all | - |
| GET /admin | ✗ | ✗ | ✓ | - |
| DELETE /user | ✗ | ✗ | ✓ | - |
| GET /resource/:id | ✗ | ✓ own | ✓ all | ✓ |
```

---

## Checks de Autenticação

### Bypass
| Teste | Como Verificar |
|-------|----------------|
| Acesso sem token | Remover header Authorization |
| Token inválido | Token malformado ou expirado |
| Token de outro user | Usar token de user diferente |
| Acesso direto | URL guessing sem auth |

### Força Bruta
| Verificação | Esperado |
|-------------|----------|
| Rate limit em login | < 10 tentativas/min |
| Lockout | Após N falhas |
| CAPTCHA | Após falhas |
| Delay progressivo | Aumenta com falhas |

### Senhas
| Verificação | Esperado |
|-------------|----------|
| Complexidade mínima | 8+ chars, mix |
| Sem senhas comuns | Blocklist |
| Hash seguro | bcrypt, argon2 |

---

## Checks de Autorização

### Vertical (Privilege Escalation)
```
User tentando acessar endpoint de Admin
└─ GET /admin/users (com token de user normal)
   └─ Esperado: 403 Forbidden
```

### Horizontal (IDOR)
```
User A tentando acessar dados de User B
└─ GET /api/users/123/data (ID de outro user)
   └─ Esperado: 403 Forbidden
```

### Business Logic
```
User tentando ação fora do fluxo
└─ POST /checkout (sem items no carrinho)
   └─ Esperado: Validação de estado
```

---

## Testes de IDOR

| Tipo | Teste |
|------|-------|
| ID sequencial | Incrementar/decrementar ID |
| UUID | Usar UUID de outro recurso |
| Parâmetro indirect | user_id em body/query |
| Path traversal | ../other_user/resource |

---

## Checks de Session

| Verificação | Teste |
|-------------|-------|
| Regeneração | Token muda após login |
| Invalidação | Token inválido após logout |
| Timeout | Expira após inatividade |
| Concurrency | Múltiplas sessões permitidas? |
| Secure transport | HTTPS only |

---

## Template de Finding

```markdown
## AUTH-XXX: [Título]

**Tipo:** IDOR | Broken AuthZ | Broken AuthN | Session
**Severidade:** High
**Endpoint:** GET /api/users/:id/data

### Descrição
Usuário A consegue acessar dados de usuário B
alterando o parâmetro ID na URL.

### Steps to Reproduce
1. Login como user A (id: 100)
2. Acessar GET /api/users/100/data (sucesso)
3. Alterar para GET /api/users/101/data
4. Dados de user B retornados

### Impacto
Acesso não autorizado a dados de outros usuários.

### Recomendação
Verificar ownership do recurso no backend:
```python
if resource.owner_id != current_user.id:
    raise Forbidden()
```
```

---

## RBAC/ABAC Checks

### RBAC (Role-Based)
- [ ] Roles definidos no sistema
- [ ] Roles enforçados em todos endpoints
- [ ] Não há hardcoded role checks bypassáveis

### ABAC (Attribute-Based)
- [ ] Policies definidas
- [ ] Attributes verificados
- [ ] Contexto considerado (time, location, etc)

---

## Critérios de Qualidade

- Matriz de acesso completa
- Todos os roles testados
- IDOR verificado em endpoints com ID
- Session handling validado

---

## Token Budget

- **Sugerido:** 4K tokens
- **Máximo:** 8K tokens
- **Reduzir:** Focar em endpoints críticos, matrix resumida

---

## Fallbacks

| Situação | Ação |
|----------|------|
| Roles não documentados | Inferir do código/UI |
| Sem credenciais de teste | Pedir ou criar em dev |
| Sistema complexo | Priorizar por sensibilidade |
| Muitos endpoints | Amostrar por categoria |

---

## Red Flags Comuns

| Pattern | Risco |
|---------|-------|
| ID na URL sem validação | IDOR |
| Role check só no frontend | Bypass fácil |
| Token sem expiração | Session hijacking |
| Sem rate limit em auth | Brute force |
