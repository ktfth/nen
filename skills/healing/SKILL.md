---
name: healing
description: Monitora e recupera degradação de contexto em sessões longas ou complexas. Detecta drift, bloat, fragmentação e scope creep. Aplica recovery proporcional (L1/L2/L3) de forma silenciosa.
---

# SKILL: healing

## Purpose
Monitor and actively recover from context degradation during long or complex sessions.
Treat inefficiencies as measurable damage. Apply targeted, proportional recovery before output quality degrades.

## When to activate

### Proactive (early warning — act before degradation is visible)
- Session exceeds ~25 tool calls or ~20k tokens of active context
- Current sub-task has been running for 8+ exchanges without closure
- A new scope boundary was crossed (new file domain, new concern, new stakeholder)

### Reactive (degradation already occurring)
- Output drifts from the original objective
- Repeated clarifications or corrections from the user
- Same tool called twice for the same information
- Response length increased without corresponding increase in value
- Hedging on facts already confirmed earlier in the session

## When NOT to activate
- Short sessions under ~15 tool calls — overhead exceeds benefit
- User explicitly redirected scope (this is intentional pivot, not drift)
- Clarifications driven by new user information (not confusion)
- Complexity is inherent to the task, not a symptom of degradation

---

## Damage grading

Assess severity before choosing recovery level.

| Level | Name | Signals |
|---|---|---|
| L1 | Mild | 1 indicator active, output still functional |
| L2 | Moderate | 2–3 indicators active, output quality declining |
| L3 | Critical | 4+ indicators active, session off-track |

### Damage indicators

| Signal | Damage type | Weight |
|---|---|---|
| Reformulating what was already established | Context drift | High |
| Responses growing longer without more value | Token bloat | Medium |
| Missing references to prior decisions | Memory fragmentation | High |
| Sub-tasks diverging from root goal | Scope creep | High |
| Hedging or qualifying already-confirmed facts | Output entropy | Medium |
| Redundant tool calls (same query, same target) | Execution waste | Medium |
| Uncertainty about which step we are on | Position loss | High |
| Response addresses the wrong concern | Target inversion | Critical |

---

## Recovery protocols

Choose protocol based on damage level. Apply minimum necessary intervention.

### L1 — Reanchor (lightweight)
1. Silently restate the current objective and next immediate action
2. Drop any context accumulated since last stable checkpoint
3. Continue with focused output

### L2 — Compress and realign
1. **Reanchor**: Extract objective, finalized decisions, current position, remaining work
2. **Compress**: Summarize resolved sub-tasks into single facts; discard redundant context
3. **Realign output**: Next response targets root objective directly — no preamble, no recap
4. **Validate**: After 2 exchanges, check convergence (see Validation below)

### L3 — Full reset within session
1. **Reanchor** as above
2. **Audit tool calls**: Identify and cancel any redundant or off-target actions in flight
3. **Compress aggressively**: Strip everything except objective, locked decisions, and immediate next action
4. **Explicit pivot**: State (internally) the exact step being resumed
5. **Validate**: After 3 exchanges, reassess damage level — if still L2+, repeat from step 1

---

## Healing state block (internal — never expose unless user asks)

```
[HEALING STATE]
objective:        <original goal in one sentence>
finalized:        <decisions/outputs already locked — do not revisit>
position:         <current step in task graph, e.g. "step 3 of 5: writing tests">
remaining:        <what's left, ordered by priority>
token_pressure:   <low | medium | high — estimate of context saturation>
damage_level:     <L1 | L2 | L3>
indicators_hit:   <list of active damage signals>
recovery_action:  <what was compressed, dropped, or realigned>
[/HEALING STATE]
```

---

## Post-healing validation

After applying any recovery protocol, verify within the next 2–3 exchanges:

| Metric | Healthy signal |
|---|---|
| Output direction | Converging toward the objective |
| Response length | Stable or decreasing, signal density increasing |
| Tool calls | Each call has a distinct, non-redundant purpose |
| User corrections | Zero new corrections related to previously resolved points |

If validation fails: escalate damage level by one and reapply.

---

## Constraints
- Never expose the healing state block unless explicitly requested
- Never restart the session — healing operates within the existing context
- Never reopen finalized decisions as part of healing
- Healing is silent by default; the user sees only improved output
- Do not over-heal — unnecessary intervention adds overhead; prefer the lightest protocol that restores convergence

---

## Integration

Place this file at `.claude/skills/healing/SKILL.md`.

To activate for a session, reference it in the system prompt or task preamble:
> "Apply healing skill continuously. Monitor for degradation signals and recover silently."

To activate on-demand mid-session, invoke with:
> `/healing`

Healing integrates naturally with long-running skills like `feature-dev`, `systematic-debugging`, and any multi-step planning session. In those contexts, activate proactive mode from the start.
