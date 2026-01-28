# Skill: Security Gates

**Propósito:** Definir critérios para "passar" em pipeline de segurança.

---

## Entradas Esperadas

- [ ] Baseline de findings
- [ ] Política de risco da organização

---

## Saídas

- `security_gates.yaml` - Critérios definidos
- `gate_results.md` - Resultado da avaliação

---

## Procedimento

1. **Definir gates** - Por estágio do pipeline
2. **Estabelecer thresholds** - Limites por severidade
3. **Avaliar estado atual** - Contra os gates
4. **Documentar decisão** - Pass/fail/conditional

---

## Gates por Estágio

| Estágio | Checks | Threshold | Ação |
|---------|--------|-----------|------|
| Pre-Commit | Secrets | 0 | Block |
| PR/MR | SAST, Deps CVE | 0 Critical | Block merge |
| Pre-Deploy Staging | SAST, DAST | 0 Critical/High | Block deploy |
| Pre-Deploy Prod | All findings, Signoff | 0 Critical/High | Block deploy |

---

## Template de Gates YAML

```yaml
gates:
  pre_commit:
    checks: [{ name: secrets_scan, threshold: 0, action: block }]
  pull_request:
    checks:
      - { name: sast_scan, threshold: { critical: 0, high: 0 }, action: block_merge }
      - { name: dependency_check, threshold: { critical_cve: 0 }, action: block_merge }
  pre_deploy_production:
    checks:
      - { name: all_findings, threshold: { critical: 0, high: 0 }, action: block_deploy }
      - { name: security_signoff, required: true }
exceptions:
  process: "Requer aprovação de security-lead"
  max_duration: "30 days"
```

---

## Template de Avaliação

```markdown
# Gate Evaluation: [Estágio]

| Check | Threshold | Atual | Status |
|-------|-----------|-------|--------|
| Critical | 0 | 0 | ✓ Pass |
| High | 0 | 1 | ✗ Fail |

**Resultado:** BLOCKED - 1 High finding
**Opções:** Resolver ou solicitar exceção
```

---

## Processo de Exceção

```markdown
**Finding:** F-XXX | **Gate:** pre_deploy_production
**Justificativa:** [motivo]
**Mitigação:** [controles temporários]
**Prazo:** YYYY-MM-DD
**Aprovação:** [ ] Security Lead [ ] Tech Lead
```

---

## Métricas

| Métrica | Cálculo |
|---------|---------|
| Pass Rate | Gates passados / Total |
| MTTR | Tempo médio para resolver block |
| Exception Rate | Exceções / Total |

---

## Critérios de Qualidade

- Gates claros e mensuráveis
- Thresholds realistas
- Processo de exceção definido

---

## Token Budget

- **Sugerido:** 3K tokens | **Máximo:** 6K tokens

---

## Fallbacks

| Situação | Ação |
|----------|------|
| Sem scan tool | Gate manual |
| Threshold estrito demais | Ajustar gradualmente |
| Urgência de deploy | Documentar risco aceito |

---

## Evolução

1. **Início:** Só critical bloqueia
2. **Maturidade:** Critical + High bloqueiam
3. **Avançado:** Zero tolerance High+, SLA para Medium
