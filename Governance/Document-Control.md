---
type: reference
code: DOC-CONTROL
owner: Chief of Staff
status: active
version: 1.0
reviewed: 2026-06-08
---

# Document Control: FinCo-OS

FinCo's Quality Management System (QMS). Defines how vault documents are created, approved, reviewed and deprecated.

---

## Document hierarchy

```
Level 1, POLICIES (POL)
  What we believe. Why we do it. Owner: Leadership.
  Review: annually or upon strategic change.

Level 2, SOPs (SOP)
  How we execute processes. Who does what, when.
  Owner: Process DRI. Review: per PDCA cycle or when process changes.

Level 3, AGENTS (Agents/)
  Who executes agentically. Identity, authority, workflow, metrics.
  Owner: Ops Manager. Review: when SOP or authority changes.

Level 4, WORKFLOWS (WF)
  Detailed steps for agents. Step-by-step procedures.
  Owner: Assigned Agent / Ops Manager. Review: when agent changes.

Level 5, RECORDS
  Evidence of execution. Meetings/, Dev/Backlog/, Operations/Cadences/, Incidents/.
  Immutable. Not edited, only appended.
```

---

## Identifiers

| Type | Prefix | Example |
|---|---|---|
| Policy | `POL-NNN` | `POL-001` |
| SOP | `SOP-NNN` | `SOP-003` |
| Workflow | `WF-slug` | `WF-meeting-ingest` |
| Agent | No code, descriptive name | `Ops-Agent` |
| Decision | `DEC-YYYY-MM-DD-slug` | `DEC-2026-06-08-linear-approved` |

---

## Mandatory frontmatter

Every governance document carries this frontmatter:

```yaml
---
type: policy | sop | workflow | agent
code: POL-001                    # omit on agents and slug-based workflows
title: Document name
owner: [Role — see Company/Team.md]
status: draft | active | deprecated
version: 1.0
reviewed: YYYY-MM-DD
review_cycle: quarterly | biannual | annual | on-change
supersedes: null | POL-XXX
---
```

---

## Document lifecycle

```
CREATE → DRAFT → REVIEW → ACTIVE → REVIEW → ACTIVE → ... → DEPRECATED
         ↑            ↓
     draft        owner approval
```

1. **Create:** use template in `Governance/Templates/`. Assign code and owner.
2. **Draft:** the document exists but is not operational. Agents do not execute it.
3. **Review:** owner reviews with real execution context.
4. **Active:** operational document. Agents may execute it.
5. **Deprecated:** the document was replaced or the process disappeared. It is not deleted; it is marked `deprecated` and `supersedes` is updated in the new doc.

---

## When to update a document

| Event | Document to update |
|---|---|
| Cycle closed with identified improvement | Corresponding SOP (Act section) |
| Process executed differently than documented | SOP + relevant WF |
| Gate activated with new approval | `Company/Gates.md` + `Strategy/Decisions/` |
| New agent created or modified | `Agents/README.md` + agent file |
| New company policy | `Governance/Policies/` + `Document-Registry.md` |

---

## Document Registry

The master index of all active documents lives in `Governance/Document-Registry.md`. Every time a document is created or deprecated, the registry is updated.

---

## Quality rules

1. **No orphan document.** Every active doc has a live owner.
2. **No process without SOP.** If it repeats more than twice, it is documented.
3. **No SOP without agent.** Every repeatable SOP has an assigned agent or one in design.
4. **No record without context.** Minutes and evidence always reference the cycle or event that generated them.
5. **Version in Git.** The change history lives in the repository, not in the file name (never `SOP-001-v2-final.md`).

---

*FinCo-OS · Governance/Document-Control.md · 2026-06-08*
