# Token Policy

Regras para minimizar uso de tokens sem perder rigor.

---

## Budgets por Tipo de Skill

| Categoria | Budget Sugerido | Máximo |
|-----------|-----------------|--------|
| Intake/Scope | 2K tokens | 4K |
| Threat Model | 4K tokens | 8K |
| Code Review | 6K tokens | 12K |
| Testing Skills | 4K tokens | 8K |
| Fix/Validation | 3K tokens | 6K |
| Delivery | 4K tokens | 8K |

---

## Estratégias de Redução

### 1. Escopo Incremental
```
ERRADO: "Analise todo o código"
CERTO:  "Analise auth/*.py para SQLi"
```

### 2. Output Estruturado
```
ERRADO: Narrativa longa explicando cada detalhe
CERTO:  Tabela com: File | Line | Issue | Severity
```

### 3. Referências em vez de Repetição
```
ERRADO: Repetir definição de SQLi em cada finding
CERTO:  "Ver CWE-89. Padrão: parameterized queries."
```

### 4. Findings Agrupados
```
ERRADO: 10 findings separados para mesmo tipo de issue
CERTO:  1 finding listando 10 ocorrências
```

### 5. Checkpoint e Continuação
```
Ao atingir 70% do budget:
- Salvar estado parcial
- Listar o que falta
- Perguntar se deve continuar
```

---

## Quando Gastar Mais Tokens

Situações que justificam budget extra:
- Vulnerabilidade crítica requer explicação detalhada
- Usuário pediu mais contexto explicitamente
- Correção complexa precisa de exemplo completo
- Compliance requer documentação extensiva

---

## Métricas de Eficiência

### Densidade de Finding
```
Bom:  1 finding útil por 500 tokens
Ruim: 1 finding útil por 2000 tokens
```

### Taxa de Actionability
```
Bom:  90% dos outputs levam a ação imediata
Ruim: Maioria é informativo sem action item
```

---

## Template Econômico

### Para Findings
```markdown
**F-XXX** | severity | category
file:line | description curta
Fix: solução em 1 linha
```

### Para Checklists
```markdown
- [x] Item completo
- [ ] Item pendente: motivo
```

### Para Summaries
```markdown
## Summary
- Analyzed: X files, Y endpoints
- Found: N findings (C:1 H:2 M:3)
- Blocked: reason if any
- Next: recommended skill
```

---

## Regras de Corte

### Cortar Quando
- Informação já documentada em outro lugar
- Explicação genérica (usar referência externa)
- Detalhes não impactam decisão

### Manter Quando
- Contexto específico do projeto
- Passos de reprodução
- Evidência mínima necessária

---

## Checkpoint Format

Ao pausar por budget:
```yaml
checkpoint:
  skill: nome
  progress: 70%
  completed:
    - item 1
    - item 2
  pending:
    - item 3
    - item 4
  resume_hint: "Continuar de X"
```
