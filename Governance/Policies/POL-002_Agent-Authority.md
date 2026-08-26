---
type: policy
code: POL-002
title: Agent Authority Policy
owner: Chief of Staff
status: active
version: 1.5
reviewed: 2026-07-07
review_cycle: quarterly
supersedes: null
---

# POL-002: Agent Authority Policy

## Purpose

Define what FinCo agents may do autonomously and what requires explicit human approval. This policy protects the business while the agentic system matures.

---

## Base principle

> Agents operate within the boundaries defined by humans. Autonomy is expanded with evidence that the process works, never assumed.

The DRI model does not eliminate gates. Gates exist to protect irreversible decisions.

---

## Vault zones: Free / Gated / Blocked

**`Governance/gates.yml` is the single source of truth** for which path falls into which zone and who approves. The table below is **generated** from that file, do not edit manually (Plan B #5, 2026-07-03). To correct a zone, edit `gates.yml` and run `_Vault/Atlas/agent/tools/generate-gates-table.py`.

<!-- BEGIN:gates-table -->

| Zone | Paths | Behavior | Approver |
|---|---|---|---|
| **Free** | `Meetings/`, `Dev/Backlog/`, `Operations/Cadences/`, `Operations/Monitoring/`, `Products/*/Bitacora.md` | Immediate auto-merge via `vault-merge.sh` — no prior approval | — |
| **Gated** | `Governance/SOPs/`, `Strategy/Decisions/`, `Strategy/DRI-Roadmap-2026/DRI-Map.md`, `Agents/`, `Agents/Workflows/`, `Knowledge-Base/` | PR opened → notify `#agent-approvals` → wait for explicit approval | Chief of Staff or CEO FinCo (either one) |
| **Blocked** | `Governance/Policies/`, `Company/Gates.md`, `Company/Team.md`, `Strategy/DRI-Roadmap-2026/OKRs.md` | PR opened → explicit justification → wait for both approvals | Chief of Staff and CEO FinCo (both) |

<!-- END:gates-table -->

**Operating rule:** if an agent does not know which zone a path falls under (not listed in `gates.yml`), treat that path as Gated.

**Draft status exception:** creating or editing a Governance document in `draft` status does not cross its folder's gate, the gate applies when promoting it to `active` (see `gates.yml`, note in `Governance/SOPs/`, and autonomous authority table below).

---

## Autonomous authority: agents may do this without asking permission

| Action | Condition |
|---|---|
| Read and process any vault document | No restriction |
| Create meeting minutes from transcripts | Processed content only, no raw data |
| Write to `Meetings/` and auto-merge the PR | Free Zone; no individual debtor data |
| Generate weekly evidence packages for internal review | Aggregate metrics only |
| Create or update tasks in Linear | Without committing external dates |
| Prepare drafts of SOPs, policies, or WFs | `Draft` status, owner approves for `active` |
| Update monitoring status in `Operations/Monitoring/` | No changes to production systems |
| Add entries to the decision log | Document only, no decision-making |
| **Publish daily standup in `#finco-dev`** | Cycle-Agent, cron in Atlas runtime (VPS), status extracted from Linear, no debtor data |
| **Publish monitoring alerts in `#finco-ops`** | Monitor-Agent, technical thresholds only, no client data |
| Update the DRI Map with meeting information | Upon confirmation from Chief of Staff |

---

## Requires approval: agents do not cross these gates alone

| Action | Gate | Approver |
|---|---|---|
| Send business-impact or team messages on Slack (outside standup and technical alerts) | Explicit approval | Chief of Staff |
| Send external communications to clients or debtors | Client dispatch gate | CEO FinCo |
| Create or modify Slack channels, groups, or permissions | Channel management gate | CEO FinCo |
| Make production changes (deploy, config) | Production changes gate | CEO FinCo |
| Write to databases (Odoo, Supabase) | Odoo/DB writes gate | CEO FinCo |
| Move or export sensitive data | Data movement gate | CEO FinCo |
| Activate or change telephony configuration | Telephony gate | CEO FinCo |
| Confirm hires or third-party commitments | Hiring gate | CEO FinCo |
| Promote a document from `draft` to `active` | Document owner review | Document owner |
| Write to Gated vault zones | PR review | Chief of Staff or CEO FinCo |
| Write to Blocked vault zones | PR review + justification | Chief of Staff and CEO FinCo |
| Close a cycle formally | Check + Act completed | Chief of Staff |

---

## Escalation protocol

When an agent needs to cross a gate:

1. The agent prepares the decision package (context, proposed action, risk, alternatives).
2. Presents it to the relevant approver in `#agent-approvals` or directly.
3. Waits for explicit approval (silence does not constitute approval).
4. Documents the approval in `Strategy/Decisions/`.
5. Executes the action.
6. Leaves an audit receipt in `#agent-audit-log`.

---

## Mandatory audit receipt

Every action that required a gate must have an audit receipt in `#agent-audit-log`:

```
[Audit] <action>, <status>
Agent: <name>
Approved by: <role>
Date: YYYY-MM-DD
Action executed: <brief description>
Gate crossed: <gate name>
Evidence: <link or reference>
```

---

## Changelog

- **v1.5 (2026-07-07):** closes residual of M7 (audit 2026-07-03), `gates.yml` and this policy named approvers by person ("Rodney"/"Alex Carver") instead of by role. Approvers in `gates.yml` migrated to roles (`Chief of Staff`, `CEO FinCo`, resolved in `Company/Team.md`); `generate-gates-table.py` derives the approver dynamically instead of hardcoding; manual rows in "Requires approval" updated to role.
- **v1.4 (2026-07-03):** Plan B #5, the zones table is generated from `gates.yml` (`generate-gates-table.py`), no longer maintained manually. Company/Gates.md does NOT receive an equivalent table; it remains exclusively business gates (C1).
- **v1.3 (2026-07-03):** corrects C2 of the 2026-07-03 audit, attributes the daily standup to Cycle-Agent (not generically "Atlas via cron"), consistent with the active name since 2026-06-20.
- **v1.2 (2026-07-03):** corrects C1 of the 2026-07-03 audit, aligns §Zones with `gates.yml` (3 levels, not 2; approvers "Rodney or Alex Carver"/"Rodney and Alex Carver", not only Chief of Staff); explicitly documents the `draft` exception.
- **v1.1 (2026-06-19):** previous version.

---

*FinCo-OS · POL-002 · v1.5 · 2026-07-07*
