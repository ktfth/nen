# Skill: Fix Playbook

**Propósito:** Fornecer padrões de correção e checklist para PRs de segurança.

---

## Entradas Esperadas

- [ ] Lista de findings a corrigir
- [ ] Linguagem/framework do projeto
- [ ] Acesso ao código fonte

---

## Saídas

- `fix_plan.md` - Plano de correção priorizado
- `pr_checklist.md` - Checklist para review de PR

---

## Procedimento

1. **Priorizar findings** - Por severidade e esforço
2. **Agrupar por tipo** - Correções similares juntas
3. **Definir padrão** - Fix pattern por categoria
4. **Criar plano** - Ordem de execução
5. **Gerar checklist** - Para review de PR
6. **Validar** - Testar que fix funciona

---

## Fix Patterns por Categoria

### SQL Injection
```python
# ANTES (vulnerável)
query = f"SELECT * FROM users WHERE id = {user_id}"

# DEPOIS (seguro)
cursor.execute("SELECT * FROM users WHERE id = ?", (user_id,))
```

### XSS
```javascript
// ANTES (vulnerável)
element.innerHTML = userInput;

// DEPOIS (seguro)
element.textContent = userInput;
// OU
element.innerHTML = DOMPurify.sanitize(userInput);
```

### Command Injection
```python
# ANTES (vulnerável)
os.system(f"ping {host}")

# DEPOIS (seguro)
subprocess.run(["ping", host], shell=False, capture_output=True)
```

### Path Traversal
```python
# ANTES (vulnerável)
path = os.path.join(base_dir, user_filename)

# DEPOIS (seguro)
safe_path = os.path.realpath(os.path.join(base_dir, user_filename))
if not safe_path.startswith(os.path.realpath(base_dir)):
    raise SecurityError("Path traversal attempt")
```

### IDOR
```python
# ANTES (vulnerável)
resource = Resource.get(id=request.params['id'])

# DEPOIS (seguro)
resource = Resource.get(id=request.params['id'])
if resource.owner_id != current_user.id:
    raise Forbidden()
```

### Hardcoded Secrets
```python
# ANTES (vulnerável)
API_KEY = "sk-live-abc123..."

# DEPOIS (seguro)
API_KEY = os.environ.get("API_KEY")
if not API_KEY:
    raise ConfigError("API_KEY not set")
```

---

## Plano de Correção Template

```markdown
# Fix Plan

**Data:** YYYY-MM-DD
**Findings:** X total (C:1 H:2 M:3)

## Prioridade 1 - Críticos (SLA: 1-3 dias)

| ID | Título | Fix Pattern | Esforço |
|----|--------|-------------|---------|
| F-001 | SQLi login | Parameterized | Baixo |

## Prioridade 2 - Altos (SLA: 7-14 dias)

| ID | Título | Fix Pattern | Esforço |
|----|--------|-------------|---------|
| F-002 | IDOR profile | Ownership check | Baixo |
| F-003 | XSS comments | Output encoding | Médio |

## Prioridade 3 - Médios (SLA: 30-60 dias)

| ID | Título | Fix Pattern | Esforço |
|----|--------|-------------|---------|
| F-004 | Missing CSP | Header config | Baixo |

## Agrupamentos

Fixes que podem ser feitos juntos:
- F-001 + F-005: Ambos são SQLi, mesma correção
- F-002 + F-006: IDOR em endpoints similares
```

---

## PR Checklist

```markdown
# Security PR Checklist

## Geral
- [ ] Fix resolve o finding específico
- [ ] Fix não introduz nova vulnerabilidade
- [ ] Testes unitários adicionados/atualizados
- [ ] Sem secrets no código

## Por Tipo de Fix

### Injection
- [ ] Usa parameterized queries/prepared statements
- [ ] Input validation adicionada
- [ ] Não usa concatenação de strings

### XSS
- [ ] Output encoding aplicado
- [ ] Framework de template usado corretamente
- [ ] Não usa innerHTML com user input

### Auth/AuthZ
- [ ] Ownership verificado
- [ ] Role check no backend
- [ ] Session regenerada onde necessário

### Crypto
- [ ] Algoritmo moderno usado
- [ ] Chaves não hardcoded
- [ ] Random seguro usado

## Antes de Merge
- [ ] Code review por outro dev
- [ ] Security review (este checklist)
- [ ] Testes de regressão passam
- [ ] Deploy em staging testado
```

---

## Esforço de Correção

| Esforço | Critério |
|---------|----------|
| Baixo | < 1 hora, mudança pontual |
| Médio | 1-4 horas, múltiplos arquivos |
| Alto | > 4 horas, refactoring necessário |

---

## Critérios de Qualidade

- Fix é específico e mínimo
- Não quebra funcionalidade
- Teste comprova correção
- Documentação atualizada

---

## Token Budget

- **Sugerido:** 3K tokens
- **Máximo:** 6K tokens
- **Reduzir:** Agrupar fixes similares, referências a patterns

---

## Fallbacks

| Situação | Ação |
|----------|------|
| Fix complexo demais | Propor mitigação temporária |
| Sem acesso ao código | Documentar padrão recomendado |
| Breaking change | Planejar versão major |
| Sem testes existentes | Adicionar testes mínimos |

---

## Anti-Patterns de Fix

| Anti-Pattern | Por que é ruim |
|--------------|----------------|
| Blocklist de inputs | Incompleto, bypassável |
| Regex para sanitizar | Fácil errar |
| Encoding no lugar errado | XSS ainda possível |
| Try/catch silencioso | Esconde problemas |
| Fix só no frontend | Backend ainda vulnerável |
