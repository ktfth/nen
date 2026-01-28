# Skill: Risk Acceptance

**Propósito:** Documentar aceite formal de riscos não mitigados.

---

## Entradas Esperadas

- [ ] Finding que não será corrigido
- [ ] Justificativa do negócio
- [ ] Controles compensatórios propostos

---

## Saídas

- `risk_acceptance_[ID].md` - Documento de aceite

---

## Procedimento

1. **Identificar risco** - Finding não corrigido
2. **Documentar justificativa** - Por que não corrigir
3. **Listar controles compensatórios** - Mitigações alternativas
4. **Definir validade** - Período do aceite
5. **Obter aprovação** - Assinaturas necessárias
6. **Registrar** - Arquivo formal

---

## Quando Usar Risk Acceptance

| Cenário | Válido |
|---------|--------|
| Custo de fix > impacto do risco | ✓ |
| Sistema em decomissão | ✓ |
| Controle compensatório efetivo | ✓ |
| "Não temos tempo" | ✗ |
| "Nunca vai ser explorado" | ✗ |
| Sem controle compensatório | ✗ |

---

## Template de Risk Acceptance

```markdown
# Risk Acceptance Form

## Informações do Risco

**ID:** RA-YYYY-XXX
**Data:** YYYY-MM-DD
**Finding Relacionado:** F-XXX
**Projeto:** [nome]

## Descrição do Risco

### Vulnerabilidade
[Descrição técnica da vulnerabilidade]

### Severidade Original
| Atributo | Valor |
|----------|-------|
| Severidade | High |
| CVSS | 7.5 |
| Likelihood | Likely |
| Impact | Major |

### Impacto Potencial
[O que poderia acontecer se explorado]

## Justificativa para Aceite

### Motivo
[Por que este risco está sendo aceito ao invés de corrigido]

### Análise de Custo-Benefício
| Fator | Valor |
|-------|-------|
| Custo de remediação | [estimativa] |
| Impacto potencial | [estimativa] |
| Probabilidade | [%] |
| Risco residual calculado | [valor] |

## Controles Compensatórios

### Controles Implementados
1. **[Controle 1]**
   - Descrição: [o que faz]
   - Reduz: [likelihood/impact]
   - Responsável: [quem]

2. **[Controle 2]**
   - Descrição: [o que faz]
   - Reduz: [likelihood/impact]
   - Responsável: [quem]

### Severidade Residual
| Atributo | Antes | Depois |
|----------|-------|--------|
| Likelihood | Likely | Possible |
| Impact | Major | Moderate |
| Risco | High | Medium |

## Condições do Aceite

### Validade
- **Início:** YYYY-MM-DD
- **Fim:** YYYY-MM-DD
- **Revisão obrigatória:** YYYY-MM-DD

### Gatilhos de Reavaliação
- [ ] Mudança na exposição do sistema
- [ ] Novo exploit publicado
- [ ] Mudança regulatória
- [ ] Incidente relacionado

### Monitoramento
[Como o risco será monitorado durante o período de aceite]

## Aprovações

| Role | Nome | Data | Assinatura |
|------|------|------|------------|
| Risk Owner | | | |
| Security Lead | | | |
| Business Owner | | | |
| [CISO se Critical] | | | |

## Registro

- [ ] Documento arquivado em: [local]
- [ ] Finding atualizado com status "Accepted"
- [ ] Notificação enviada para stakeholders
- [ ] Reminder agendado para revisão
```

---

## Controles Compensatórios Comuns

| Risco | Controle Compensatório |
|-------|------------------------|
| Vuln em endpoint | WAF rule específica |
| Auth fraca | MFA obrigatório |
| Dados expostos | Criptografia em trânsito |
| Falta de logging | SIEM monitoring |
| Falta de rate limit | CDN rate limiting |

---

## Níveis de Aprovação

| Severidade | Aprovadores Mínimos |
|------------|---------------------|
| Info | Team Lead |
| Low | Team Lead |
| Medium | Security Lead + Manager |
| High | Security Lead + Director |
| Critical | CISO + Executive |

---

## Critérios de Qualidade

- Risco claramente descrito
- Justificativa é válida
- Controles compensatórios existem
- Aprovações documentadas
- Validade definida

---

## Token Budget

- **Sugerido:** 3K tokens
- **Máximo:** 6K tokens
- **Reduzir:** Template pré-preenchido

---

## Fallbacks

| Situação | Ação |
|----------|------|
| Sem controle compensatório | Recusar aceite |
| Aprovador indisponível | Escalar para backup |
| Risco crítico | Exigir CISO approval |
| Prazo expirado | Forçar reavaliação |

---

## Anti-Patterns

| Anti-Pattern | Por que é ruim |
|--------------|----------------|
| Aceite perpétuo | Risco nunca reavaliado |
| Sem controle compensatório | Risco mantido integralmente |
| Aprovador sem autoridade | Documento inválido |
| Justificativa vaga | Não é decision-based |
