---
type: guide
code: GUIDE-001
title: Dev Assistance Agents Guide
owner: Lead Engineering
status: active
version: 1.0
reviewed: 2026-06-23
review_cycle: on-change
related_sop: SOP-005
---

# Dev Assistance Agents Guide

## Purpose

Establish mandatory and recommended practices for using assistance agents (Claude Code or others) in FinCo repositories, ensuring that each work session has the necessary context to operate with quality and coherence.

## Scope

Applies to all development team members who use AI agents in any active FinCo repository. Complements [[SOP-005_Development-Governance]] (Rule 8).

---

## What the vault is and why it matters

An AI agent has no memory between sessions. Without an external memory system, each conversation starts from zero: questions repeat, decisions are lost, open bugs are forgotten, and the agent works without business context.

The **vault** (`_vault/`) is a folder of Markdown files that acts as the project's memory. The agent reads it at the start of each session and updates it when closing. With an active vault, the agent knows what was done yesterday, what is pending, what decisions were made and how the business works.

---

## Mandatory requirements

### 1. Active vault per repository

Every FinCo repository where work is done with an agent must have an operational `_vault/`. If it does not exist when starting the first session, the agent creates it automatically during bootstrap.

Minimum vault structure:

```
_vault/
├── status-board.md
├── projects/
│   └── <proyecto>.md
├── daily-log/
│   ├── YYYY-MM-DD-<proyecto>.md
│   └── TAREAS PENDIENTES/
│       └── <proyecto>-tasks.md
├── decisions/
└── insights/
```

### 2. FinCo-OS cloned in the personal vault

Each member's personal Obsidian vault must include FinCo-OS as a **read-only folder**, cloned from the official repository. This ensures the agent has access to the current version of:

- Corporate policies and SOPs
- Roles and responsibilities structure
- FinCo official branding
- Business decisions and roadmap

**How to clone it the first time:**

```bash
# Inside the root folder of your personal Obsidian vault
git clone https://github.com/FinCo/finco-os.git FinCo-OS
```

**How to keep it updated (at the start of each work session):**

```bash
cd FinCo-OS && git pull
```

> The FinCo-OS folder inside the vault is read-only. Do not modify it directly, changes to the operating system are made in the official repository and distributed via `git pull`.

---

## Recommendations (not mandatory)

### Personal vault skeleton

Mateo Herrera and Marco Santos developed a vault skeleton that has proven to work well for agent work on FinCo projects. It is recommended as a starting point.

The full technical spec is at:

```
finco-conductor/docs/OBSIDIAN-MEMORY-SYSTEM.md
```

The document includes:
- Vault folder structure
- Read-at-start and write-at-session-close protocol
- Installation artifacts: `obsidian-archivist` agent, `obsidian-sync` skill, `SessionStart` hook
- Templates for daily-log, decisions and project notes
- Troubleshooting

**The internal structure of the personal vault is individual autonomy territory.** Each member adapts the skeleton to their way of working and interacting with the agent. There is no single correct way.

### Recommended session protocol

When starting a work session with an agent:

1. `git pull` in the FinCo-OS folder (updated corporate context).
2. Ask the agent to read the project vault (`_vault/`).
3. Work.
4. When closing, ask the agent to update the vault (daily-log, pending tasks, decisions if any).

---

## Attribution

The vault skeleton and persistent memory system (`SISTEMA_MEMORIA_OBSIDIAN.md`) were developed by **Mateo Herrera** and **Marco Santos** (June 2026) as part of the SOP-005 revision. The recommendation to integrate FinCo-OS as a cloned folder is by **Rodney Ramirez**.

---

## Review

Reviewed when the team proposes changes to the vault system or when new assistance tools are adopted. Owner: Lead Engineering.

---

*FinCo-OS · GUIDE-001 · v1.0 · 2026-06-23*
