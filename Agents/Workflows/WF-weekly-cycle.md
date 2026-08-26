---
type: workflow
code: WF-weekly-cycle
owner: Chief of Staff
agent: Cycle-Agent
sop: SOP-001
status: active
version: 2.1
reviewed: 2026-07-03
---

# WF-weekly-cycle: Weekly Development Cycle

**Executed by:** [[Cycle-Agent]] (automation) + Chief of Staff (human facilitation) · **Reference:** `SOP-001_Weekly-Cycle`

> **v2.0 (2026-07-03):** replaces v1.0, which described the model abolished on 2026-06-10 (human standup Tuesday 8:45am, PLAN until 1:00). Corrects C3 / finding M5 from the 2026-07-03 audit, survived uncorrected since the previous audit (2026-06-19).

---

## 1: Cycle opening (automatic, Saturday)

**Cron:** `0 2 * * 6` (Saturday 2am UTC / 10pm RD Friday) · **Script:** `cycle-plan-capture.py`

1. Cycle-Agent queries Linear: cycle with `isNext: true` for team `FinCo`.
2. Extracts all cycle issues with DRI, status, and priority.
3. Writes `Dev/Cycles/YYYY-WNN/Plan.md`: exact mirror of Linear.
4. Commit + push to the vault.

**Rule for the Product Owner:** issues for the next cycle must be assigned in Linear before Friday at 10pm RD (during the PLAN block of Check/Act/Plan, see step 5). If not assigned by that time, they won't be included in the `Plan.md` captured that night.

## 2: Active cycle (Monday–Thursday)

### 2a: Daily agentic standup (automatic)

**Cron:** `0 22 * * 1-4` (Mon/Tue/Wed/Thu 10pm UTC / 6pm RD) · **Script:** `standup-daily.py`

1. Calculates lookback window: Monday = 84h (since previous Thursday); Tuesday–Thursday = 26h.
2. Queries Linear: issues for team `FinCo` with `updatedAt > since`.
3. Groups by status: ✅ Completed · 🔄 In Progress · 📋 Not Started · 🔍 In Review. Ignores Backlog and Canceled.
4. Posts in `#finco-dev`.

### 2b: Daily deadline reminder (automatic)

**Cron:** `0 14 * * *` (daily 2pm UTC / 10am RD) · **Script:** `deadline-reminder.py`

1. Queries Linear: overdue issues (last 90 days, not completed/canceled) and issues due today or tomorrow.
2. Posts report with two sections (🔴 Overdue / ⏰ Due soon) in `#finco-all`.

### 2c: Brief human standup (Tuesday morning, 15-20 min)

No agent, team checkpoint. Blockers and priority of the day, without demos or retrospective. If anything relevant arises, [[Meeting-Agent]] processes the transcript → `Meetings/Team-Syncs/`.

### 2d: Team Sync (Thursday morning, 60 min)

No agent, human alignment with demos. [[Meeting-Agent]] processes the transcript → `Meetings/Team-Syncs/`. Commitments go to Linear, not resolved solely in the meeting.

## 3: Cycle close (Friday 3:00pm, 90 min) / Check/Act/Plan

### CHECK (0:00–0:20)
1. Cycle-Agent presents the Linear summary: closed vs. committed issues.
2. Each DRI gives one sentence: what was left pending and why.
3. Record in the Cycle Review minutes (`Meetings/Cycle-Reviews/YYYY-WNN.md`), Decisions/Context section.

### ACT (0:20–0:35)
1. Did any process fail repeatedly? → propose an SOP update.
2. Was any definition ambiguous? → propose an update to `Dev/Methodology.md`.
3. Systemic blocker? → create an issue in Linear with assigned DRI.
4. Record improvements in the same Cycle Review minutes, Blockers/Decisions section.

### PLAN (0:35–1:30)
1. Each DRI confirms their 2 issues for the following week (WIP limit = 2 per person).
2. Verify DRI assigned on each issue, without DRI, it doesn't enter the cycle.
3. Issues remain assigned in Linear before 10pm RD (see step 1, capture to `Plan.md` is automatic that night).

---

## Expected records per cycle (`Dev/Cycles/YYYY-WNN/`)

```
Dev/Cycles/2026-W25/
  ├── Plan.md       ← auto-generated (Saturday, cycle-plan-capture.py)
  └── Standups.md   ← notes from Team Sync/brief standup (if Meeting-Agent processed something)
```

> **Deprecated 2026-07-03:** `Review.md` per cycle was removed, Check/Act lives in the Cycle Review minutes (`Meetings/Cycle-Reviews/YYYY-WNN.md`), which Meeting-Agent already generates. Maintaining both was a redundant bureaucratic layer (finding M6 from the audit, Rodney's decision).

---

## References

- `Governance/SOPs/SOP-001_Weekly-Cycle.md`: the full process, cadence, and principles.
- `Agents/Cycle-Agent.md`: infrastructure of the 3 crons (scripts, secrets, logs, manual commands).
- `Agents/Meeting-Agent.md`: processing of Brief Standup and Team Sync transcripts.

---

## Changelog

- **v2.1 (2026-07-03):** deprecates `Review.md` per cycle (finding M6), Check/Act now lives solely in the Cycle Review minutes.
- **v2.0 (2026-07-03):** complete rewrite, mirrors the model in effect since 2026-06-10/20 (agentic standup + Thursday Team Sync + Check/Act/Plan with PLAN until 1:30; automatic cycle opening on Saturday, not Friday).
- **v1.0 (2026-06-08):** original version, described the Tuesday 8:45am standup model, abolished 2026-06-10. Reference archived, not the file (this same path was rewritten in place).

---

*FinCo-OS · Agents/Workflows/WF-weekly-cycle.md · v2.1 · 2026-07-03*
