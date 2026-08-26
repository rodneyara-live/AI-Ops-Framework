---
type: sop
code: SOP-004
title: DRI Weekly Evidence
owner: Chief of Staff
status: active
version: 1.0
reviewed: 2026-06-08
review_cycle: quarterly
supersedes: null
---

# SOP-004: DRI Weekly Evidence

## Purpose

Ensure that each DRI produces verifiable weekly evidence of their component. The system does not operate on trust or verbal updates; it operates on written proof.

> "Evidence is weekly. Not verbal. No status calls. Written proof." (FinCo DRI Map)

---

## Cadence

Every Friday, before Check/Act/Plan (3pm), each DRI produces their evidence package. The Ops-Agent generates the consolidated report for Chief of Staff review.

---

## Evidence package structure per DRI

```markdown
## [DRI Name]: Evidence Week [YYYY-WNN]

**Components under my responsibility:** [list]

### Metrics (3–5 maximum)
| Metric | Value this week | Baseline | Status |
|---|---|---|---|
| [metric 1] | [value] | [reference] | 🟢/🟡/🔴 |

### Achievements
- [What was completed this week with evidence]

### Active issues
- [Issue], DRI: [[name]]: Days open: N, Next action: [action]

### Decisions made
- [Decision], Approved by: [[name]]: Date: YYYY-MM-DD

### Risks
- [Risk], Probability: High/Medium/Low, Impact: High/Medium/Low, Mitigation: [action]

### Blockers requiring escalation
- [Blocker], Blocked since: YYYY-MM-DD, I need: [what]

### Next actions (following week)
- [ ] [Action] 📅 YYYY-MM-DD, DRI: [[name]]
```

---

## Automatic escalation rule

If a blocker has been unresolved for more than 7 days, the Ops-Agent automatically escalates it in `#finco-ops` with context and resolution options.

---

## Destination

Evidence packages go to `Strategy/DRI-Roadmap-2026/DRI-Evidence/YYYY-WNN.md`, consolidated by the Ops-Agent.

---

## Suggested metrics per DRI (startup)

| DRI | Starting metrics |
|---|---|
| Chief of Staff, Ops | % DRI with weekly evidence, blockers >7 days, cycles closed on time |
| Innovation & Dev Analyst, Nova | Nova response rate, completed conversations, errors |
| Director of Operations, Strategy Engine | % knowledge transfer completed, SOPs created |
| Operations Manager, Odoo/Legal | % Odoo setup completed, legal processes mapped |
| Lead Engineering, StrategyPreflight | % spec completed, tests defined |

---

## Executed by

Ops-Agent (`Agents/Ops-Agent.md`). See `Agents/Workflows/WF-evidence-package.md` for detailed steps.

---

*FinCo-OS · SOP-004 · 2026-06-08*
