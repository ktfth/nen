# Skill: Report

**Propósito:** Gerar relatório executivo e técnico consolidado.

---

## Entradas Esperadas

- [ ] Todos os findings documentados
- [ ] Resultados de validação
- [ ] Scope definition
- [ ] Métricas coletadas

---

## Saídas

- `security_report.md` - Relatório completo
- `executive_summary.md` - Resumo para gestão

---

## Procedimento

1. **Consolidar findings** - Todas as fases
2. **Calcular métricas** - Por severidade, categoria
3. **Escrever executive summary** - 1 página
4. **Detalhar findings** - Seção técnica
5. **Listar recomendações** - Priorizadas
6. **Incluir anexos** - Evidências, referências

---

## Estrutura do Relatório

```markdown
# Security Assessment Report

## Metadata
- Projeto: [nome]
- Data: YYYY-MM-DD
- Versão: 1.0
- Classificação: Confidencial

## Table of Contents
1. Executive Summary
2. Scope
3. Methodology
4. Findings Summary
5. Detailed Findings
6. Recommendations
7. Appendices
```

---

## Executive Summary Template

```markdown
# Executive Summary

## Objetivo
Avaliação de segurança do [projeto] para identificar vulnerabilidades
e recomendar correções.

## Escopo
- Aplicação: [nome/versão]
- Componentes: [lista]
- Período: [datas]
- Tipo: [code review / pentest / audit]

## Resultados Principais

| Severidade | Quantidade |
|------------|------------|
| Critical | X |
| High | Y |
| Medium | Z |
| Low | W |
| Info | V |

## Principais Riscos
1. **[Risco 1]** - [Impacto em 1 linha]
2. **[Risco 2]** - [Impacto em 1 linha]
3. **[Risco 3]** - [Impacto em 1 linha]

## Recomendações Prioritárias
1. [Ação 1] - [Prazo sugerido]
2. [Ação 2] - [Prazo sugerido]
3. [Ação 3] - [Prazo sugerido]

## Conclusão
[2-3 sentenças sobre postura geral e próximos passos]
```

---

## Findings Summary Table

```markdown
## Findings Summary

| ID | Título | Severidade | Status | SLA |
|----|--------|------------|--------|-----|
| F-001 | SQLi in login | Critical | Open | 3d |
| F-002 | XSS stored | High | Fixed | - |
| F-003 | Missing CSP | Medium | Open | 30d |
```

---

## Detailed Finding Template

```markdown
## F-XXX: [Título]

**Severidade:** Critical | High | Medium | Low | Info
**CWE:** CWE-XXX
**OWASP:** [A01:2021 etc]
**Status:** Open | Fixed | Accepted

### Descrição
[O que é a vulnerabilidade]

### Localização
- Arquivo: `path/to/file`
- Linha: XX
- Endpoint: `/api/endpoint`

### Impacto
[O que um atacante pode fazer]

### Reprodução
1. Passo 1
2. Passo 2
3. Passo 3

### Evidência
[Screenshot ou snippet sanitizado]

### Recomendação
[Como corrigir]

### Referências
- [Link 1]
- [Link 2]
```

---

## Seção de Recomendações

```markdown
## Recommendations

### Immediate (0-7 days)
| # | Ação | Finding | Esforço |
|---|------|---------|---------|
| 1 | Fix SQLi in login | F-001 | Baixo |

### Short-term (7-30 days)
| # | Ação | Finding | Esforço |
|---|------|---------|---------|
| 2 | Implement CSP | F-003 | Médio |

### Long-term (30-90 days)
| # | Ação | Finding | Esforço |
|---|------|---------|---------|
| 3 | Security training | - | Alto |
```

---

## Métricas do Report

```markdown
## Metrics

### Coverage
- Files reviewed: X of Y (Z%)
- Endpoints tested: A of B (C%)
- Threat scenarios: N covered

### Findings Distribution
- By Severity: [gráfico texto]
  Critical: ██ 2
  High:     ████ 4
  Medium:   ██████ 6

- By Category:
  Injection: 3
  Auth: 2
  XSS: 2
  Config: 1

### Trend (se histórico disponível)
- Previous: 15 findings
- Current: 8 findings
- Change: -47%
```

---

## Critérios de Qualidade

- Executive summary em 1 página
- Findings completos e reproduzíveis
- Recomendações são actionable
- Classificação de confidencialidade

---

## Token Budget

- **Sugerido:** 4K tokens
- **Máximo:** 8K tokens
- **Reduzir:** Tabelas, templates pré-definidos

---

## Fallbacks

| Situação | Ação |
|----------|------|
| Muitos findings | Agrupar por categoria |
| Sem métricas históricas | Omitir trend |
| Audiência mista | Separar exec/técnico |
| Prazo curto | Focar em High+ |

---

## Anexos Opcionais

- A: Scope Agreement
- B: Methodology Details
- C: Tool Outputs
- D: Evidence Screenshots
- E: Glossary
- F: References
