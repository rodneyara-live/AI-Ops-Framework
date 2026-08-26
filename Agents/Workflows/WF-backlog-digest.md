---
type: workflow
code: WF-backlog-digest
owner: Chief of Staff
agent: Backlog-Agent
status: active
version: 1.0
reviewed: 2026-06-12
---

# WF-backlog-digest: Weekly Backlog Digest Generation

Executed by [[Backlog-Agent]]. Trigger: Thursday afternoon, or when the Chief of Staff requests it before the planning session.

---

## Steps

### 1: Read sources

| Source | What to extract |
|---|---|
| `Dev/Backlog-Inbox.md` | **First**: all PROD items accumulated since the last digest |
| `#finco-backlog` (Slack) | All messages since the last digest |
| Week's Team Sync minutes | Actionable PROD items without a Linear issue (only if not already in Inbox) |
| `Strategy/DRI-Roadmap-2026/DRI-Map.md` | DRI actions past due or due this week |

After reading the Inbox, empty it (remove processed items).

### 2: Classify

Each item goes into one of these categories:

- **Urgent**: active blocker or direct impact on a client today
- **High priority**: must enter the next cycle
- **Medium priority**: backlog for future cycles
- **Infrastructure / technical debt**: not a feature, system health
- **Operational actions**: management tasks, do not enter Linear

### 3: Detect duplicates

Compare each item against open issues in Linear (Backlog + Todo + In Progress). Mark duplicates in the digest with `⚠️ possible duplicate of PAY-XXX`.

### 4: Generate digest

Create `Dev/Backlog/WNN-Digest-YYYY-MM-DD.md` with format:

```
### [AREA-NNN] Descriptive title
**Source:** who requested it and where
**Why it matters:** consequence of not doing it
**Suggested DRI:** [[Person]]
**Linear Action:** Epic → Issue (suggestion)
```

Areas: `PROD` · `INF` · `OPS` · `GTM`

### 5: Publish summary in Slack

Publish a 5-10 line summary in `#finco-backlog` autonomously.

### 6: Gate: creating issues in Linear

Only create issues in Linear when the Chief of Staff explicitly confirms, before or during the Friday planning session.

---

## Output

- `Dev/Backlog/WNN-Digest-YYYY-MM-DD.md`: full digest
- Draft message for `#finco-backlog` (draft for approval)
- List of created issues in Linear (post-approval)

---

## Success criteria

The team arrives at the Friday Cycle Review with the backlog organized, without wasting time debating what exists. Every Linear issue has enough context for any dev to understand it without asking.

---

*FinCo-OS · Agents/Workflows/WF-backlog-digest.md · v1.0 · 2026-06-12*
