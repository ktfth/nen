# Skill: Scope Guardrails

**Propósito:** Definir limites, obter autorização e identificar dados sensíveis antes de iniciar.

---

## Entradas Esperadas

- [ ] Intake summary completo
- [ ] Lista de ambientes disponíveis
- [ ] Nível de autorização concedido
- [ ] Restrições específicas (se houver)

---

## Saídas

- `scope_definition.yaml` - Escopo formal acordado
- `authorization_record.md` - Registro de autorização

---

## Procedimento

1. **Definir ambientes** - Quais podem ser analisados (dev/stg/prod)
2. **Estabelecer limites** - O que está dentro/fora do escopo
3. **Classificar autorização** - Read-only, passive test, active test
4. **Identificar dados sensíveis** - PII, PHI, PCI, segredos
5. **Documentar restrições** - Rate limits, janelas de manutenção
6. **Obter confirmação** - Registro formal de aceite
7. **Gerar scope document** - Referência para todas as skills

---

## Níveis de Autorização

| Nível | Permite | Não Permite |
|-------|---------|-------------|
| read_only | Análise de código, configs | Qualquer requisição |
| passive_test | Requests normais, navegação | Payloads maliciosos |
| active_test | Fuzzing controlado, PoCs | DoS, exfiltração real |

---

## Template de Scope

```yaml
# scope_definition.yaml
scope:
  id: "SCOPE-[YYYYMMDD]-[ID]"
  project: "[nome]"

  authorization:
    level: "[read_only|passive_test|active_test]"
    granted_by: "[nome]"
    date: "[YYYY-MM-DD]"
    expires: "[YYYY-MM-DD ou N/A]"

  environments:
    allowed:
      - dev
      - staging
    forbidden:
      - production

  targets:
    include:
      - "src/**"
      - "api/**"
    exclude:
      - "vendor/**"
      - "node_modules/**"
      - "*.test.*"

  sensitive_data:
    types:
      - PII
      - credentials
    handling: "sanitize_all_outputs"

  restrictions:
    - "No requests to production"
    - "Max 10 req/min to staging"
    - "No automated scanners"

  out_of_scope:
    - "Third-party integrations"
    - "Mobile apps"
    - "Infrastructure"
```

---

## Template de Autorização

```markdown
# Authorization Record

**Date:** YYYY-MM-DD
**Project:** [nome]
**Authorized By:** [nome/cargo]

## Scope Summary
- Environments: [lista]
- Authorization Level: [nível]
- Duration: [período]

## Explicit Permissions
1. [permissão 1]
2. [permissão 2]

## Explicit Restrictions
1. [restrição 1]
2. [restrição 2]

## Acknowledgment
This authorization covers defensive security review only.
No exploitation beyond proof-of-concept will be performed.

---
Confirmed: [sim/não]
```

---

## Critérios de Qualidade

- Autorização documentada antes de qualquer teste
- Ambientes claramente separados
- Dados sensíveis identificados
- Restrições respeitáveis definidas

---

## Token Budget

- **Sugerido:** 2K tokens
- **Máximo:** 4K tokens
- **Reduzir:** Templates pré-preenchidos, confirmações binárias

---

## Fallbacks

| Situação | Ação |
|----------|------|
| Sem autorização formal | Operar em read_only |
| Ambiente indefinido | Assumir mais restritivo |
| Dúvida sobre escopo | Perguntar antes de agir |
| Dados sensíveis não mapeados | Tratar tudo como sensível |

---

## Red Flags (Pausar e Escalar)

- Pedido para testar produção sem autorização formal
- Solicitação de bypass de controles
- Pressão para ignorar restrições
- Dados de clientes reais em scope
