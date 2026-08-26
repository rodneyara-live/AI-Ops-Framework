---
type: orientation
updated: 2026-07-03
---

# FinCo-OS: START HERE

Operating system of [[FinCo]]. Entry point for the team, Claude/Cowork, and Atlas.

> **Management philosophy:** PDCA. Every process has a Plan → Do → Check → Act cycle. No process without an owner, no owner without evidence, no evidence without a record.

---

## For agents: canonical source and startup

**FinCo-OS is the single operational source of truth for any [[FinCo]] agent.**

This rule applies to: Atlas (Slack), Cowork, Claude Code, and any future agent. Every surface that interacts with [[FinCo]] uses the vault as its primary source, not base training, not prior conversation memory, not inferences.

**Startup protocol for any agent:**
1. Read this file (`START-HERE.md`) for general orientation
2. Read the corresponding agent file in `Agents/` for your role and authority
3. Consult the canonical file on the topic before responding (table below)
4. If the information is not in the vault, say so explicitly, do not improvise

**Atlas identity:** defined in the agent's workspace configuration, canonical source, versioned in the vault. Repo rules for agents: `AGENTS.md` (root).

---

## Ecosystem: which system does what

| Surface | Role | Updates |
|---|---|---|
| **FinCo-OS** (this vault) | Durable source of truth. Policies, SOPs, agents, workflows, operational context, decisions, evidence. | Rodney + Atlas |
| **Linear** | Live work status. Active issues, cycles, assignments, WIP. | Each DRI, nobody writes for someone else |
| **Slack** | Conversation, blockers, alerts, agential standup. Trigger for automations. | Entire team + Atlas |
| **GitHub** | Code, PRs, commits, reviews. GitHub Issues are not used for management. | Devs, each PR references its Linear issue |
| **Google Meet / transcripts** | Raw meeting source. Not promoted directly, processed first. | Automatic |
| **Odoo CRM** | Operational source of client data, portfolios, cases. | Operations team |

## What does NOT go in this vault

- Secrets, tokens, API keys, only in local `.env` (git-ignored)
- Debtor or client data: names, IDs, balances, histories
- Raw meeting transcripts, only processed minutes
- Alex Carver's private notes, his vault is separate
- Database dumps or logs with sensitive data
- Bilateral meeting minutes, never processed or stored. They are private by design; Meeting-Agent ignores them completely (see `WF-meeting-ingest`)

## Promotion rule

```
Conversation / Meeting
      ↓
  Slack (processed draft)
      ↓
  Minutes (summary + decisions + owners) [if meeting]
      ↓
  Promote to SOP / decisions (if applicable)
      ↓
  Rodney's gate before promoting
```

---

## Writing conventions

### Languages

| Element | Language | Examples |
|---|---|---|
| Folder names | English | `Governance/`, `Agents/Workflows/` |
| File names | English | `WF-meeting-ingest.md`, `POL-001_Data-Handling.md` |
| Frontmatter keys | English | `type`, `owner`, `status`, `reviewed` |
| Frontmatter values | English | `active`, `draft`, `policy`, `workflow` |
| File content | English | prose, descriptions, instructions |

**Exception:** proper names of people and products are written as-is (`Rodney-Ramirez`, `Conductor`).

### Mandatory frontmatter in Governance documents

```yaml
type: policy | sop | workflow | agent | job-description
code: POL-001 | SOP-001 | WF-<descriptive-slug> | AGT-001 | JD-001
owner: [Role]
status: draft | active | deprecated
version: 1.0
reviewed: YYYY-MM-DD
```

> **WF codes:** unlike POL/SOP/AGT/JD, Workflows use a descriptive slug (`WF-meeting-ingest`, `WF-backlog-digest`) instead of `WF-NNN` numbering, the slug is more readable than an opaque number.

### Format

- **WikiLinks** always for people (`[[Rodney-Ramirez]]`), products (`[[Conductor]]`) and companies (`[[FinCo]]`). Exception: inside Mermaid code blocks.
- **Mermaid:** line breaks with `<br>`, never `\n`.
- **Reference project:** `Agents/Ops-Agent.md` is the most complete document in the vault. If in doubt about agent format, review it.

### Document hierarchy

```
Policies (POL)    ← what we believe and commit to
      ↓
SOPs              ← how we operate processes
      ↓
Agents            ← who executes, with what authority
      ↓
Workflows         ← detailed steps for agents
      ↓
Records           ← evidence of execution
```

### Chain propagation

When you touch any of these files, **update their indexes in the same commit**:

| If you modify… | Also update… |
|---|---|
| `Agents/` (new agent or status change) | `Agents/README.md`, `Governance/Document-Registry.md`, `START-HERE.md`: agent table |
| `Governance/` (new POL/SOP/WF/JD) | `Governance/Document-Registry.md` |

**Superseded files:** move to an archive directory with note `> Superseded by [file], YYYY-MM-DD`. Do not leave the old file coexisting with the new one.

### Mandatory checklist when renaming or deprecating

When renaming an agent, channel, script, folder or process, or when deprecating any file:

1. `grep -rn "<old-name>"` across the entire vault **before considering the change done**.
2. Fix each reference found (or explicitly confirm why it is left, e.g., immutable historical record).
3. If the file is deprecated: archive it in the **same commit** (do not leave it coexisting with a "deprecated" banner in its original location).
4. One single commit for the rename + all its references, do not split into multiple commits that could be left incomplete.

### Commit when done

Every session with at least one write ends with commit and push:

```bash
cd "/path/to/vault/FinCo-OS"
git add -A
git commit -m "type(scope): description"
git push
```

Format: `feat(ops): DRI-Map updated` · `docs(agents): Ops-Agent v1.1` · `fix(sop): SOP-001 fix cadence`.

---

## Folder structure

| Folder | Content |
|---|---|
| `Governance/` | QMS: policies, SOPs, job profiles, document control, templates |
| `Dev/` | Methodology, development cycles, backlog digest |
| `Agents/` | Definition of FinCo-OS agents |
| `Agents/Workflows/` | Detailed step-by-step procedures |
| `Operations-Samples/` | Monitoring templates and review samples (anonymized) |

---

## Quick navigation

| I'm looking for… | Go to… |
|---|---|
| Job profile | `Governance/Roles/` |
| How the weekly cycle runs | `Governance/SOPs/SOP-001_Weekly-Cycle.md` |
| How to ingest a meeting | `Agents/Meeting-Agent.md` |
| Platform health today | `Operations-Samples/Monitoring/Daily-Checklist.md` |
| GitHub / branch rules | `Governance/SOPs/SOP-005_Development-Governance.md` |
| Atlas, status and configuration | `Agents/Atlas-Agent.md` |

---

## Available agents

Before executing any operation, activate the correct agent by reading its file:

| Agent | When to use it | File |
|---|---|---|
| **Ops-Agent** | Day-to-day operations, DRI Map, weekly evidence, weekly executive report (Monday) | `Agents/Ops-Agent.md` |
| **Meeting-Agent** | You have a Google Meet transcript | `Agents/Meeting-Agent.md` |
| **Monitor-Agent** | Check platform health | `Agents/Monitor-Agent.md` |
| **Knowledge-Agent** | Extract operational knowledge from conversations | `Agents/Knowledge-Agent.md` |
| **Backlog-Agent** | Generate weekly backlog digest for planning | `Agents/Backlog-Agent.md` |
| **OKR-Agent** | Propose OKR scorecard updates (under Rodney/Alex Carver Gate) | `Agents/OKR-Agent.md` |
| **Dev-Agent** | Development governance and PR review | `Agents/Dev-Agent.md` |
| **Atlas-Agent** | Agential CoS via Slack, status, nudges, escalations | `Agents/Atlas-Agent.md` |
| **Compliance-Agent** *(draft)* | Script review, compliance control status, incidents | `Agents/Compliance-Agent.md` |
| **Support-Agent** *(draft)* | There is a support ticket or client incident | `Agents/Support-Agent.md` |
| **Cycle-Agent** | Cycle automation: daily standup, due-date alerts, weekly Plan.md | `Agents/Cycle-Agent.md` |

---

## Workflow index

| WF | Operation | Executed by |
|---|---|---|
| `WF-meeting-ingest` | Transcript → Minutes + DRI updates | Meeting-Agent |
| `WF-weekly-cycle` | Automatic cycle opening (Saturday) + daily standups + Check/Act/Plan close (Friday) | Cycle-Agent |
| `WF-evidence-package` | Generate weekly evidence package per DRI | Ops-Agent |
| `WF-dri-update` | Update DRI Map from any event | Ops-Agent |
| `WF-monthly-review` | Prepare operating review with leadership | Ops-Agent |
| `WF-knowledge-extract` | Distill meeting/transcript/document into Knowledge-Base on-demand | Knowledge-Agent |
| `WF-backlog-digest` | Generate backlog digest for Friday session | Backlog-Agent |
| `WF-dev-governance` | PR check + weekly development governance scan | Dev-Agent |
| `WF-google-drive-transcript-watch` | Detect and route new transcripts in Drive | Atlas |
| `WF-vault-write-github` | Write to vault via PR on GitHub | any |
| `WF-ops-report` | Daily operational report + on-demand analytics | Atlas |
| `WF-weekly-executive-report` | Weekly executive report (Monday) → review storage + branded HTML | Ops-Agent / Atlas |

---

## Weekly PDCA cycle

```
MON-THU 6pm           TUESDAY              THURSDAY            FRIDAY
Standup-Agent         Brief standup        Team Sync           Check/Act/Plan 3pm
posts in #dev         ~15 min              60 min              90 min
status from Linear    Quick checkpoint     Demos + progress    Check → Act
                      no demos             + decisions         PO plans in Linear
                                           ↓
                                           Minutes/Team-Syncs/
```

**Agential standup (Mon/Tue/Wed/Thu 6pm):** Cycle-Agent publishes team status in `#dev` from Linear. No manual meeting.

**Brief standup (Tuesday morning):** 15-20 min quick checkpoint. Blockers and priority of the day. No demos.

**Team Sync (Thursday morning):** Human alignment session. The team shows progress and demos. Meeting-Agent processes the transcript.

**Check/Act/Plan (Friday 3pm):**
- 0:00–0:20 → CHECK: what did we deliver vs. what we promised?
- 0:20–0:35 → ACT: what do we improve? → update SOPs if applicable
- 0:35–1:30 → PLAN: next week commitments → Linear + cycle Plan.md

---

## Invariant rules

1. **WikiLinks always** for people, products and companies. Exception: inside Mermaid blocks.
2. **Never raw transcripts** in the vault. Only processed minutes.
3. **Never sensitive debtor data** in any vault file.
4. **Every session with writes** ends with `git commit` and `git push`.
5. **Active gates**: verify current approval rules before any external action.
6. **Records are immutable**: review archives are not edited. Only appended.
7. **One canonical file per topic**: if the topic already has an entry, update that file. Do not create a new one.
8. **Roles, not people**: SOPs, policies and workflows name roles (Lead Engineering, Operations Manager, Chief of Staff), never people. The role → person mapping lives in the team roster.
9. **Always use templates**: Before creating any SOP, policy, agent, workflow or job description, read the corresponding template in `Governance/Templates/`. Do not create governance documents from scratch.

---

*FinCo-OS · START-HERE · 2026-07-03*
