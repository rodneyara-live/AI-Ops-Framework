---
type: sop
code: SOP-001
title: Weekly Cycle
owner: Chief of Staff
status: active
version: 1.3
reviewed: 2026-07-03
review_cycle: quarterly
supersedes: null
---

# SOP-001: Weekly Cycle

## Purpose

Define how FinCo's weekly development cycle operates. One cycle = one week. The pace is set by the work, not by meetings.

## Scope

Development team (see `Company/Team.md` for current role → person mapping). Facilitated by the Chief of Staff.

---

## Cycle structure

| Moment | When | Duration | Purpose | PDCA |
|---|---|---|---|---|
| **Active cycle** | Monday–Thursday | Continuous | Work in Linear, updates in Slack `#finco-dev` | **Do** |
| **Agentic standup** | M/Tu/W/Th 6pm RD (Cycle-Agent) | Automatic | Cycle-Agent posts team status in `#finco-dev` from Linear | **Do** |
| **Brief standup** | Tuesday morning | 15-20 min | Quick checkpoint: blockers and daily priority; no demos | **Do** |
| **Team Sync** | Thursday morning | 60 min | Demos + progress + decisions needing the full team | **Do** |
| **Check/Act/Plan** | Friday 3:00pm | 90 min | Review the closing cycle and plan the next one | **Check → Act → Plan** |

> **PDCA Note:** **Do** is execution, everything from Monday to Thursday. The Friday session is not a "closing" but the moment where the cycle is reviewed (Check), improved (Act), and renewed (Plan). The team never closes; it always opens the next one.

**Time zone:** Dominican Republic (UTC-4, no DST).

> **Decision 2026-06-10 (Alex Carver + Rodney):** The Tue/Thu standups are replaced by: (1) daily agentic standup via Atlas in Slack, and (2) a single weekly human alignment meeting. The status standup objective is covered by Atlas; the objective of keeping the team united and aligned is covered by the Team Sync.

---

## Daily agentic standup: Atlas

Atlas posts the team status from Linear in `#finco-dev` each morning:
- Issues in progress per DRI
- Issues closed since the previous day
- Active blockers
- Alerts for items with no movement >2 days

*No human intervention required. When Atlas is fully operational.*

---

## Brief standup (Tuesday morning): 15-20 min

Quick team checkpoint. Short, no demos, no deliberation.

**Purpose:** Today's blockers, immediate priority, does anyone need anything from anyone?

**Rules:**
- No demos. No design decisions. No retrospectives.
- Meeting-Agent may process the transcript if there is something relevant → `Meetings/Team-Syncs/`.
- If there are no blockers or news: 10 minutes and back to work.

---

## Team Sync (Thursday morning): 60 min

Human alignment meeting. The most important touchpoint of the week.

**Purpose:**
- The team shows what they built: live product demos.
- Technical design decisions needing the team are discussed.
- Systemic blockers are escalated.
- Maintain cohesion and direct communication in a remote-first team.

**Participants:**
- Core dev team. The Director of Operations is welcome, can join anytime.

**Rules:**
- Meeting-Agent processes the transcript → note in `Meetings/Team-Syncs/`.
- Specific commitments (who does what) go to Linear, not resolved solely in the meeting.
- Fixed duration: 60 min.

---

---

## Check/Act/Plan (Fri 3:00pm): 90 min

**Time structure:**

**0:00–0:20, CHECK (what did we deliver vs. what we committed?)**
- The Cycle-Agent presents the Linear summary: issues closed vs. committed.
- Each DRI gives one sentence: what was left pending and why.
- Cycle metric: what did we promise and what did we deliver? (a single metric).

**0:20–0:35, ACT (what do we improve?)**
- Is there a process that failed repeatedly? → update SOP.
- Is there an ambiguous definition that caused delays? → clarify in `Dev/Methodology.md`.
- Is there a systemic blocker? → create issue in Linear with assigned DRI.
- Improvements identified are recorded in the Cycle Review minutes (`Meetings/Cycle-Reviews/YYYY-WNN.md`).

**0:35–1:30, PLAN (what do we commit to next week?)**
- Each DRI confirms their 2 issues for the week (WIP limit = 2 per person).
- The Cycle-Agent creates the new cycle's `Plan.md` in `Dev/Cycles/`.
- No DRI assigned = does not enter the cycle.

---

## Demo to management: monthly + on-demand

Demo session before the full FinCo/GroupCo staff. Fixed monthly + on-demand if there is something urgent to show.

**Purpose:** Executive visibility of progress, business feedback, alignment with the sponsor (Executive Sponsor).

**Format:** Live product demo + period metrics. Not an operational meeting; it is a showcase.

---

## Cycle file structure

Each cycle lives in `Dev/Cycles/[YYYY-WNN]/`:

```
Dev/Cycles/2026-W25/
  ├── Plan.md       ← cycle commitments (auto-generated on Saturday)
  └── Standups.md   ← Team Sync notes (Thursday), if Meeting-Agent processed something
```

> **Deprecated 2026-07-03:** Per-cycle `Review.md` was removed from the pattern; it was a redundant bureaucratic layer with the Cycle Review minutes (`Meetings/Cycle-Reviews/YYYY-WNN.md`), which already captures delivered vs. promised and improvements (Check/Act). Decision by Rodney (finding M6 from the 2026-07-03 audit).

---

## Methodology principles

1. **2-week cycles did not work culturally** (learning from previous experience). FinCo uses 1-week cycles.
2. **WIP limit = 2 per person.** Maximum 2 active simultaneous issues. With 5 devs, no more than 10 items in flight. What does not fit goes to the backlog.
3. **DRI per issue.** Without an assigned owner, it does not enter the cycle.
4. **Blockers visible.** Blockers are escalated in Slack `#finco-dev`: they are not hidden in board columns.
5. **One metric per cycle.** At closing: what did we promise and what did we deliver? No velocity tracking or burndown.
6. **Atlas does the agentic standup.** Pull from Linear each morning → post in `#finco-dev` with the day's status. *When Atlas is live.*

---

## Executed by

Chief of Staff with support from Meeting-Agent (transcripts) and Cycle-Agent (daily standup + Plan.md cycle). See `Agents/Workflows/WF-weekly-cycle.md` for detailed steps.

---

## Changelog

- **v1.3 (2026-07-03):** deprecates per-cycle `Review.md` (finding M6), redundant with Cycle Review minutes.
- **v1.2 (2026-07-03):** fixes C2 from the 2026-07-03 audit, replaces obsolete name "Standup-Agent" with "Cycle-Agent" (renamed since 2026-06-20).
- **v1.1 (2026-06-11):** previous version.

---

*FinCo-OS · SOP-001 · v1.3 · 2026-07-03*
