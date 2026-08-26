---
type: workflow
code: WF-evidence-package
owner: Chief of Staff
agent: Ops-Agent
sop: SOP-004
status: active
version: 1.0
reviewed: 2026-06-08
---

# WF-evidence-package: Weekly Evidence Package

**Executed by:** Ops-Agent · **Reference:** SOP-004_DRI-Weekly-Evidence

---

## Frequency

Every Thursday, generate for review on Friday before Check/Act/Plan.

## Steps

1. **Read** `Strategy/DRI-Roadmap-2026/DRI-Map.md`: current status of all components
2. **For each active DRI**, gather:
   - What progressed this week? (evidence in minutes, decisions, or Linear issues)
   - Metrics available? (Monitor-Agent can provide channel data)
   - Open issues? How many days have they been open?
   - Blockers? How long?
3. **Identify** blockers older than 7 days without resolution → mark for escalation
4. **Generate** the file `Strategy/DRI-Roadmap-2026/DRI-Evidence/YYYY-WNN.md` with the consolidated data
5. **Present** to [[Chief of Staff]] for review before Check/Act/Plan

## Destination

`Strategy/DRI-Roadmap-2026/DRI-Evidence/YYYY-WNN.md`

## Automatic escalation

If there is a blocker >7 days: prepare a message for `#finco-ops` with:
- What is blocked
- How many days
- What is needed to unblock it
- Who must act
