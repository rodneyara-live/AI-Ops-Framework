---
type: sop
code: SOP-002
title: Meeting Ingest
owner: Chief of Staff
status: active
version: 1.3
reviewed: 2026-07-03
review_cycle: on-change
supersedes: null
---

# SOP-002: Meeting Ingest

## Purpose

Define how FinCo meetings are processed from the Google Meet transcript to the vault minutes and DRI Map updates.

## Scope

All FinCo meetings with transcript available in Google Workspace.

---

## Fundamental rules

**Raw transcripts never enter the vault.** Only processed minutes: with sensitive data removed, commitments identified, and structured format.

**Cycle Planning, task extraction disabled.** The Cycle Planning meeting (Friday afternoon, led by the Product Owner) is processed as minutes following the standard flow, but the step of classifying and extracting actionables (OPS→Linear / PROD→Backlog-Inbox) is completely omitted. The backlog is already built during the week (Slack messages + Thursday digest); the meeting decisions go directly to Linear through the PO.

---

## Process flow

```
Google Meet → Transcript (Gemini) → Meeting-Agent processes → Minutes.md → Vault
                                                          ↓
                                               DRI Map updated if applicable
                                                          ↓
                                               Strategy/Decisions/ if there were decisions
```

---

## Timing

| Step | Target time |
|---|---|
| Transcript available in Drive | Automatic post-meeting (Gemini) |
| Minutes generated | < 30 min post-meeting |
| DRI Map updated | Same session |
| Decisions recorded | Same session |

---

## Minutes structure

Every minutes file follows this structure:

```markdown
# Minutes: [Meeting type], [Meeting title], YYYY-MM-DD

**Type:** Brief Standup | Team Sync | Cycle Planning | Cycle Review | Operating Review | Client Meeting | Compliance Meeting | Technical Ad-hoc | Operational Ad-hoc

> Bilateral generates no minutes; it is completely ignored (see `WF-meeting-ingest` Step 1b).
**Participants:** [[Name1]], [[Name2]]
**Facilitated by:** [[Name]]

## Context
One sentence about the meeting purpose.

## Decisions made
- Decision 1, made by [[Name]]
- Decision 2, made by [[Name]]

## Commitments and actionables
- [ ] [[Name]]: Verb + object + context 📅 YYYY-MM-DD
- [ ] [[Name]]: Verb + object + context 📅 YYYY-MM-DD

## Blocker identified
- Blocker 1, DRI: [[Name]]

## Next meeting
[Date and purpose if applicable]
```

---

## Minutes destination by type

| Meeting type | Vault destination | Task extraction |
|---|---|---|
| Brief standup (Tue morning) | `Meetings/Team-Syncs/YYYY-MM-DD.md` (only if there are relevant decisions or blockers) | ✅ OPS + PROD |
| Team Sync (Thu morning) | `Meetings/Team-Syncs/YYYY-MM-DD.md` | ✅ OPS + PROD |
| Cycle Planning (Fri afternoon) | `Meetings/Cycle-Reviews/YYYY-WNN-Cycle-Planning.md` | ❌ None |
| Cycle Review (Fri) | `Meetings/Cycle-Reviews/YYYY-WNN.md` | ✅ OPS + PROD |
| Operating Review (monthly) | `Meetings/Operating-Reviews/YYYY-MM.md` | ✅ OPS + PROD |
| Bilateral / 1:1 | None, completely ignored | ❌ None |
| Client Meeting | `Meetings/Client-Meetings/[client]-YYYY-MM-DD.md` | ✅ OPS + PROD |
| Compliance Meeting | `Meetings/Compliance/YYYY-MM-DD.md` | ✅ OPS + PROD |
| Technical Ad-hoc | `Meetings/Ad-hoc-Dev/YYYY-MM-DD-[brief-topic].md` | ✅ OPS + PROD |
| Operational Ad-hoc | `Meetings/Ad-hoc-Ops/YYYY-MM-DD-[brief-topic].md` | ✅ OPS + PROD |

---

## Executed by

Meeting-Agent (`Agents/Meeting-Agent.md`). See `Agents/Workflows/WF-meeting-ingest.md` for detailed steps.

---

## Changelog

- **v1.3 (2026-07-03):** Plan B #10, bilaterals no longer generate minutes nor are saved in `_Local/` (that folder never exists on the VPS). They are completely ignored; Rodney decides manually whether to process them.
- **v1.2 (2026-07-03):** fixes C5 from the 2026-07-03 audit, template types and destinations table updated to the current 10 types (previously listed 5, with "Team Sync (Tue)" describing the abolished model). Cycle Planning and Cycle Review canonized in `Meetings/Cycle-Reviews/`; Bilateral corrected to `_Local/Bilaterals/`; Compliance Meeting points to `Meetings/Compliance/` (folder created).
- **v1.1 (2026-06-19):** previous version.

---

*FinCo-OS · SOP-002 · v1.3 · 2026-07-03*
