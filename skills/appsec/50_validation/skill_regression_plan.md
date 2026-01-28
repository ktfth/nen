# Skill: Regression Plan

**Propósito:** Definir testes de regressão e validação pós-fix para garantir que correções funcionam.

---

## Entradas Esperadas

- [ ] Lista de fixes aplicados
- [ ] Findings originais
- [ ] Acesso ao ambiente de teste

---

## Saídas

- `regression_plan.md` - Plano de testes de regressão
- `validation_results.md` - Resultados da validação

---

## Procedimento

1. **Listar fixes** - O que foi corrigido
2. **Definir casos de teste** - Por fix
3. **Testar negativo** - Confirmar que vuln não existe
4. **Testar positivo** - Confirmar que funcionalidade ok
5. **Documentar resultados** - Pass/fail por caso
6. **Identificar gaps** - O que não foi testado

---

## Casos de Teste por Tipo

### SQL Injection Fix
```markdown
**Teste Negativo (Vuln não existe):**
1. Enviar payload `' OR 1=1--` no campo
2. Esperado: Input rejeitado ou tratado como literal
3. Resultado: [ ] Pass [ ] Fail

**Teste Positivo (Funcionalidade ok):**
1. Enviar input válido normal
2. Esperado: Operação funciona corretamente
3. Resultado: [ ] Pass [ ] Fail
```

### XSS Fix
```markdown
**Teste Negativo:**
1. Enviar `<script>alert(1)</script>`
2. Esperado: Script escapado ou removido
3. Verificar: View source, não executa

**Teste Positivo:**
1. Enviar texto com caracteres especiais válidos
2. Esperado: Exibido corretamente (escaped)
```

### IDOR Fix
```markdown
**Teste Negativo:**
1. Login como User A
2. Tentar acessar recurso de User B
3. Esperado: 403 Forbidden

**Teste Positivo:**
1. Login como User A
2. Acessar recurso próprio de User A
3. Esperado: 200 OK, dados corretos
```

### Auth Bypass Fix
```markdown
**Teste Negativo:**
1. Acessar endpoint sem autenticação
2. Esperado: 401 Unauthorized

**Teste Positivo:**
1. Acessar com token válido
2. Esperado: 200 OK
```

---

## Template de Regression Plan

```markdown
# Regression Plan

**Data:** YYYY-MM-DD
**Fixes Validando:** F-001, F-002, F-003

## Casos de Teste

### F-001: SQL Injection in /api/search

| # | Tipo | Teste | Input | Esperado | Resultado |
|---|------|-------|-------|----------|-----------|
| 1 | Neg | Payload SQLi | `' OR 1=1--` | Erro/escaped | |
| 2 | Neg | Union attack | `UNION SELECT` | Bloqueado | |
| 3 | Pos | Query normal | `valid search` | Resultados | |

### F-002: XSS in /comments

| # | Tipo | Teste | Input | Esperado | Resultado |
|---|------|-------|-------|----------|-----------|
| 1 | Neg | Script tag | `<script>` | Escaped | |
| 2 | Neg | Event handler | `onerror=` | Escaped | |
| 3 | Pos | Normal text | `Hello World` | Exibido | |

## Ambiente
- URL: [staging URL]
- Credenciais: [test user info]
- Data do teste: YYYY-MM-DD
```

---

## Template de Resultados

```markdown
# Validation Results

**Data:** YYYY-MM-DD
**Testador:** [nome]

## Sumário

| Fix | Testes | Pass | Fail | Status |
|-----|--------|------|------|--------|
| F-001 | 3 | 3 | 0 | ✓ Validado |
| F-002 | 3 | 2 | 1 | ✗ Retest |

## Detalhes

### F-001: VALIDADO
Todos os testes passaram. SQLi não mais reproduzível.

### F-002: RETEST NECESSÁRIO
Teste #2 falhou. Event handler `onerror` ainda executa.
Evidência: [screenshot]

## Próximos Passos
- [ ] Reabrir F-002
- [ ] Aplicar fix adicional
- [ ] Re-testar
```

---

## Checklist de Validação

```markdown
## Checklist Pré-Validação
- [ ] Fix deployado em staging
- [ ] Ambiente acessível
- [ ] Credenciais de teste prontas
- [ ] Casos de teste definidos

## Checklist Por Fix
- [ ] Teste negativo executado
- [ ] Teste positivo executado
- [ ] Evidência capturada
- [ ] Resultado documentado

## Checklist Pós-Validação
- [ ] Todos fixes críticos validados
- [ ] Falhas documentadas
- [ ] Tickets atualizados
- [ ] Ready for production
```

---

## Critérios de Qualidade

- Todo fix tem teste negativo
- Funcionalidade verificada
- Resultados documentados
- Falhas têm ação definida

---

## Token Budget

- **Sugerido:** 3K tokens
- **Máximo:** 6K tokens
- **Reduzir:** Tabelas compactas, agrupar testes similares

---

## Fallbacks

| Situação | Ação |
|----------|------|
| Sem ambiente de teste | Validar via code review |
| Fix não deployado | Documentar, pedir deploy |
| Teste inconclusivo | Detalhar, pedir review |
| Regressão encontrada | Abrir novo finding |

---

## Automation Hints

Quando possível, automatizar testes:

```yaml
# Exemplo: test cases para runner
tests:
  - name: sqli_f001
    type: negative
    endpoint: /api/search
    method: POST
    body: {"q": "' OR 1=1--"}
    expect:
      status: [400, 200]
      body_not_contains: "SQL syntax error"

  - name: normal_search
    type: positive
    endpoint: /api/search
    method: POST
    body: {"q": "valid search"}
    expect:
      status: 200
```
