---
type: workflow
code: WF-dri-update
owner: Chief of Staff
agent: Ops-Agent
status: active
version: 1.0
reviewed: 2026-06-08
---

# WF-dri-update: DRI Map Update

**Executed by:** Ops-Agent

---

## When to execute

- Post-meeting with information that changes a component's status
- When [[Chief of Staff]] indicates there is new information
- Weekly, when generating the evidence package

## Steps

1. **Read** `Strategy/DRI-Roadmap-2026/DRI-Map.md` in full
2. **Identify** which components have new information:
   - Did the status change? (🔵 → 🟣, 🟣 → 🏆, etc.)
   - Is there a new next action with a date?
   - Was a blocker resolved?
   - Is there new evidence?
3. **Prepare a draft** of changes (do not apply without confirmation)
4. **Present** to [[Chief of Staff]]: "I propose these changes to the DRI Map, do you confirm?"
5. **Apply** approved changes
6. **Update** the `updated:` field in the DRI Map frontmatter

## Rule

Never apply changes to the DRI Map without confirmation from [[Chief of Staff]]. The DRI Map is the operational source of truth, an erroneous change affects management decisions.
