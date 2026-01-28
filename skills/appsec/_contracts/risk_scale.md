# Risk Scale

Definições objetivas de severidade, likelihood e impact.

---

## Severidade (CVSS Simplificado)

| Nível | Score | Critério |
|-------|-------|----------|
| Critical | 9.0-10.0 | RCE, bypass total de auth, leak massivo de dados |
| High | 7.0-8.9 | Privilege escalation, SQLi com exfiltração, XSS stored admin |
| Medium | 4.0-6.9 | XSS reflected, IDOR limitado, info disclosure sensível |
| Low | 1.0-3.9 | Info disclosure não-sensível, missing headers, verbose errors |
| Info | 0.1-0.9 | Best practice, não explorável diretamente |

---

## Likelihood (Probabilidade de Exploração)

| Nível | Critério |
|-------|----------|
| Certain | Exploit público, trivial, sem autenticação |
| Likely | Exploit requer baixo skill, info disponível |
| Possible | Requer conhecimento específico ou cadeia de ataques |
| Unlikely | Condições muito específicas, difícil reproduzir |
| Rare | Teórico, sem PoC conhecido |

---

## Impact (Impacto no Negócio)

| Nível | Critério |
|-------|----------|
| Catastrophic | Breach massivo, multa regulatória, fim do negócio |
| Major | Perda financeira significativa, dano reputacional sério |
| Moderate | Interrupção de serviço, perda de dados limitada |
| Minor | Inconveniência, correção simples |
| Negligible | Sem impacto real no negócio |

---

## Matriz de Risco

```
              IMPACT
              Neg  Min  Mod  Maj  Cat
LIKELIHOOD  ┌────┬────┬────┬────┬────┐
Certain     │ L  │ M  │ H  │ C  │ C  │
Likely      │ L  │ M  │ H  │ H  │ C  │
Possible    │ I  │ L  │ M  │ H  │ H  │
Unlikely    │ I  │ L  │ L  │ M  │ H  │
Rare        │ I  │ I  │ L  │ L  │ M  │
            └────┴────┴────┴────┴────┘

I = Info, L = Low, M = Medium, H = High, C = Critical
```

---

## SLA de Remediação Sugerido

| Severidade | SLA (dias úteis) |
|------------|------------------|
| Critical | 1-3 |
| High | 7-14 |
| Medium | 30-60 |
| Low | 90 |
| Info | Backlog |

---

## Exemplos Práticos

### Critical
- SQLi em endpoint público permitindo dump do banco
- RCE via deserialização insegura
- Hardcoded credentials de produção no código

### High
- IDOR permitindo acesso a dados de outros usuários
- XSS stored em área administrativa
- JWT sem validação de assinatura

### Medium
- CSRF em ações não-críticas
- Rate limiting ausente em login
- Headers de segurança faltando

### Low
- Verbose error messages
- Server version disclosure
- Cookies sem flag Secure em dev

### Info
- Comentários no código com TODOs de segurança
- Bibliotecas desatualizadas sem CVE conhecida
