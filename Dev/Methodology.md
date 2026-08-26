---
type: reference
updated: 2026-07-03
owner: Chief of Staff
---

# Development Methodology: FinCo

How the FinCo technical team works. Weekly cycles, no Scrum ceremony, no management overhead.

---

## The 6 principles

### 01: Rhythm: 1-week cycles

One cycle = one week. Three human touchpoints: Brief standup on Tuesday, Team Sync on Thursday, Check/Act/Plan on Friday. Everything else is continuous flow in Linear.

> Why 1 week and not 2: 2-week cycles did not work culturally in previous experience. The weekly rhythm maintains visibility without increasing overhead.

### 02: Board: 3 columns, nothing more

**Backlog → In Progress → Done.**

No columns to hide problems. If something is blocked, the DRI escalates it in `#finco-dev`: visible, not buried in an "On Hold" column.

### 03: Focus: WIP limit of 5 per person

Maximum 5 active issues per person simultaneously. With 5 active devs, no more than 25 things in flight at once. What does not fit in that limit goes to the backlog, not to anyone's plate.

### 04: Ownership: DRI per issue

Each issue has exactly one owner. The DRI **creates, moves, and closes** their own issue, nobody does it for them. No assigned DRI = does not enter the cycle. No issue created before Tuesday's standup = the work does not formally exist for that week.

> The Ops Lead is not the team's secretary. Creating issues for others does not scale and creates a dependency that goes against the DRI model.

### 05: Agent: agential standup

The Cycle-Agent extracts status from Linear and posts to `#finco-dev` every Mon/Tue/Wed/Thu at 6pm RD:
- What is in progress per DRI
- What was completed since the last standup
- What is about to start

Nobody writes a standup manually. Operational since 2026-06-20.

### 06: Metric: one per cycle

At Friday's close: what did we promise and what did we deliver? No velocity tracking, no burndown charts. Delivery is the only metric that matters at this stage.

---

## Meeting cadence

| Meeting | When | Duration | Purpose |
|---|---|---|---|
| Brief standup | Tuesday morning | 15-20 min | Quick checkpoint, blockers, priority of the day, no demos |
| Team Sync | Thursday morning | 60 min | Demos + progress + decisions that need the entire team |
| Check/Act/Plan | Friday 3:00pm | 90 min | Cycle closing Check + Act (improvements), PO plans the next cycle in Linear |

**Cadence in effect since 2026-06-19** (decision by [[Rodney-Ramirez]] and [[Daniela-Rivas]]): Brief standup on Tuesday; Team Sync with demos on Thursday morning. Allows more progress to show on Thursdays and aligns with Friday planning.

**Total human meetings per week: maximum 2.5 hours.** Daily status is published by Atlas in `#finco-dev` from Linear.

For branch and PR governance, see `Governance/SOPs/SOP-005_Development-Governance.md`.

---

## How to escalate blockers

1. The DRI identifies the blocker
2. Posts it in `#finco-dev` with context: what is needed, from whom, by when
3. If not resolved before the next standup → mentions it at the standup
4. If it takes more than 3 days → Ops-Agent escalates it in the weekly evidence package
5. If it takes more than 7 days → automatic escalation to [[Rodney-Ramirez]]

**Blockers are not hidden on the board. They are escalated.**

---

## Linear: how we use the board

| Element | Usage |
|---|---|
| Issues | Tasks with assigned DRI, effort estimate, and clear description |
| Cycles | One cycle = one week. Starts Monday, ends Friday. |
| Backlog | Everything that does not fit in the current cycle |
| Labels | Use for type: feature, bug, infra, chore, spike |
| Priority | P1 (this week), P2 (next 2 weeks), P3 (backlog) |

The vault stores the **context** of development decisions. Linear stores the **tasks**.

---

## What we learned and will not repeat

- Hard Scrum (full ceremonies) did not work culturally with this team
- 2-week cycles lose visibility in a fast-moving startup
- Without WIP limit, everyone has 8 things "in progress" and nothing advances
- Without DRI per issue, issues circulate without closing

---

*FinCo-OS · Dev/Methodology.md · 2026-06-12*
