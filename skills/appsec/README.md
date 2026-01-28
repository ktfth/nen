# AppSec Skills Orchestrator

Pacote de skills para conduzir trabalho de AppSec defensivo ponta-a-ponta.

## Índice de Skills

| Fase | Skill | Arquivo |
|------|-------|---------|
| Contracts | I/O Schema | `_contracts/io_schema.md` |
| Contracts | Risk Scale | `_contracts/risk_scale.md` |
| Contracts | Evidence Rules | `_contracts/evidence_rules.md` |
| Contracts | Token Policy | `_contracts/token_policy.md` |
| 00 Intake | Intake | `00_intake/skill_intake.md` |
| 00 Intake | Scope Guardrails | `00_intake/skill_scope_guardrails.md` |
| 10 Threat Model | Threat Model Lite | `10_threat_model/skill_threat_model_lite.md` |
| 10 Threat Model | Attack Surface Map | `10_threat_model/skill_attack_surface_map.md` |
| 20 Review | Code Review | `20_review/skill_code_review.md` |
| 20 Review | Dependency Review | `20_review/skill_dependency_review.md` |
| 20 Review | Secrets Review | `20_review/skill_secrets_review.md` |
| 30 Testing | SAST Baseline | `30_testing/skill_sast_baseline.md` |
| 30 Testing | DAST Safety | `30_testing/skill_dast_safety.md` |
| 30 Testing | AuthZ/AuthN Checks | `30_testing/skill_authz_authn_checks.md` |
| 30 Testing | Injection Checks | `30_testing/skill_injection_checks.md` |
| 30 Testing | Business Logic | `30_testing/skill_business_logic.md` |
| 40 Fixes | Fix Playbook | `40_fixes/skill_fix_playbook.md` |
| 40 Fixes | Secure Defaults | `40_fixes/skill_secure_defaults.md` |
| 50 Validation | Regression Plan | `50_validation/skill_regression_plan.md` |
| 50 Validation | Security Gates | `50_validation/skill_security_gates.md` |
| 60 Delivery | Report | `60_delivery/skill_report.md` |
| 60 Delivery | Risk Acceptance | `60_delivery/skill_risk_acceptance.md` |
| 60 Delivery | Handoff | `60_delivery/skill_handoff.md` |

---

## Decision Tree

```
START
  │
  ├─ Projeto novo ou primeira análise?
  │    └─ SIM → Intake → Scope Guardrails → Threat Model Lite → Attack Surface
  │
  ├─ Code review solicitado?
  │    └─ SIM → Secrets Review → Code Review → Dependency Review
  │
  ├─ Testes de segurança solicitados?
  │    └─ SIM → SAST Baseline → AuthZ/AuthN → Injection Checks
  │    └─ Se autorizou DAST → DAST Safety
  │
  ├─ Vulnerabilidade encontrada?
  │    └─ SIM → Fix Playbook → Secure Defaults → Regression Plan
  │
  └─ Entrega final?
       └─ SIM → Security Gates → Report → Handoff
```

---

## Modos de Execução

### Modo Lean (Mínimo Viável)
Sequência: `Intake → Secrets Review → Code Review → Fix Playbook → Report`

Use quando:
- Prazo apertado
- Escopo pequeno (< 5 arquivos críticos)
- Revisão pontual de PR/MR

### Modo Full (Análise Completa)
Sequência completa por fases: `00 → 10 → 20 → 30 → 40 → 50 → 60`

Use quando:
- Auditoria de segurança formal
- Novo produto/feature crítica
- Compliance requer evidências

---

## Protocolo de Campos Desconhecidos

Quando faltar contexto:

1. **Detectar lacuna** - Identifique o dado ausente
2. **Listar 3 hipóteses** - Baseado em padrões comuns
3. **Pedir 2 dados mínimos** - Perguntas diretas ao usuário
4. **Avançar com plano seguro** - Escolha o caminho mais conservador

Exemplo:
```
LACUNA: Tipo de banco de dados desconhecido
HIPÓTESES: (1) PostgreSQL (2) MySQL (3) MongoDB
DADOS MÍNIMOS: Arquivo de config? String de conexão?
PLANO SEGURO: Assumir SQL relacional, verificar injection genérico
```

---

## Checklist de Não-Falhas

Antes de finalizar qualquer fase:

- [ ] **Segredos** - Nenhum secret exposto em logs/outputs
- [ ] **Logs** - Outputs não contêm dados sensíveis
- [ ] **Envs** - Ambientes claramente identificados (dev/stg/prod)
- [ ] **Rate Limits** - Testes não causam DoS acidental
- [ ] **Evidência** - Screenshots/logs sanitizados
- [ ] **Repro** - Passos de reprodução documentados sem payloads ofensivos

---

## Formato Padrão de Execução

Cada etapa deve seguir:

```markdown
## [Nome da Skill]

### Goal
O que será alcançado nesta execução.

### Context
- Projeto: [nome]
- Escopo: [arquivos/endpoints]
- Restrições: [limites acordados]

### Actions
1. Ação executada
2. Ação executada
3. ...

### Output
- Artefato gerado: [link/referência]
- Findings: [resumo]

### Next
- Próxima skill recomendada: [nome]
- Bloqueios: [se houver]
```

---

## Regras Gerais

1. **Nunca executar ataques** sem autorização explícita documentada
2. **Preferir análise estática** sobre testes ativos quando possível
3. **Documentar tudo** - Decisões, findings, não-findings
4. **Escalar quando necessário** - Se encontrar crítico, pausar e comunicar
5. **Manter escopo** - Não expandir sem autorização

---

## Quick Start

```bash
# 1. Rodar intake para entender o projeto
skill: 00_intake/skill_intake.md

# 2. Definir guardrails
skill: 00_intake/skill_scope_guardrails.md

# 3. Escolher modo (Lean ou Full) e seguir sequência
```
