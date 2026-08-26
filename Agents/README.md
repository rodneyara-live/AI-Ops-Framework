---
type: reference
updated: 2026-07-03
---

# FinCo-OS Agents

Directory of available agents. Before executing any operation, activate the correct agent by reading its full file.

---

## When to use which agent

| Situation | Agent |
|---|---|
| I need to update the DRI Map, generate weekly evidence, prepare the operating review | Ops-Agent |
| I have a Google Meet transcript to process | Meeting-Agent |
| It is Friday and I need to close the cycle (Check/Act) | Meeting-Agent (transcript) |
| I want to verify if the platform is working correctly | Monitor-Agent |
| I have a conversation and want to extract operational knowledge | Knowledge-Agent |
| There was a meeting/transcript/document with valuable insights, ask Atlas to distill it into Knowledge-Base | Knowledge-Agent (`WF-knowledge-extract`, on-demand) |
| I need the backlog digest for Friday's planning session | Backlog-Agent |
| There is a pending PR or I want to review development governance | Dev-Agent |
| The team needs to interact with the vault via Slack (agenic CoS) | Atlas-Agent |
| There is a support ticket or client incident | Support-Agent *(draft)* |
| Review scripts, Law 74-25 controls, or compliance incidents | Compliance-Agent *(draft)* |
| See the team's progress in Linear / know if someone has a task due | Cycle-Agent |
| Keep the OKR scorecard up to date (proposes; Rodney/Alex Carver approve) | OKR-Agent |

---

## Full fleet

> Generated from the frontmatter of `Agents/*.md`: do not edit by hand. To correct status/owner/version, edit the agent file and run the agent registry generation script.

<!-- BEGIN:agents-table -->

| Code | Agent | Status | Owner | Version |
|---|---|---|---|---|
| AGT-001 | Ops-Agent | active | Chief of Staff | 1.3 |
| AGT-002 | Meeting-Agent | active | Chief of Staff | 2.0 |
| AGT-003 | Cycle-Agent | active | Chief of Staff | 2.1 |
| AGT-004 | Monitor-Agent | active | Chief of Staff | 1.1 |
| AGT-005 | Knowledge-Agent | active | Chief of Staff | 1.2 |
| AGT-006 | Backlog-Agent | active | Chief of Staff | 0.2 |
| AGT-007 | Dev-Agent | active | Lead Engineering | 0.1 |
| AGT-008 | Atlas-Agent | active | Chief of Staff | 2.0 |
| AGT-009 | Support-Agent | **draft** | Developer | **0.2** |
| AGT-010 | Compliance-Agent | **draft** | Compliance Manager | **0.2** |
| AGT-011 | OKR-Agent | active | Chief of Staff | 1.0 |

<!-- END:agents-table -->

---

## How agents work

Each agent file defines:

1. **Identity and mission**: who I am at FinCo
2. **Autonomous authority**: what I can do without asking permission (see POL-002)
3. **Gates**: what requires approval before executing
4. **Triggers**: what events activate me
5. **Workflow**: my steps when activated
6. **Success metrics**: how I know I did my job well

---

## Relationship between agents

```
Atlas-Agent (agenic CoS, conversational vault interface)
Ops-Agent (operations coordinator)
    ├── Meeting-Agent    ← transcripts → minutes + actionables (incl. Cycle Review minute)
    ├── Monitor-Agent    ← platform health
    ├── Knowledge-Agent  ← operational knowledge extraction
    └── Backlog-Agent    ← weekly digest for planning session
Dev-Agent (development governance, independent)
Support-Agent (client tickets, draft)
Compliance-Agent (applicable local debt-collection regulations controls, draft)
Cycle-Agent (daily standup + deadline alerts + Plan.md, autonomous via cron)
```

Ops-Agent is the default entry point for operations. For development, Dev-Agent. For conversational team interaction, Atlas-Agent.

---

*FinCo-OS · Agents/README.md · 2026-07-03 (versions/owners resynced, M7 audit)*
