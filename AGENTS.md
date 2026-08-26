# AGENTS.md: Working rules for agents in FinCo-OS

This repository (**FinCo-OS**) is the single operational source of truth for [[FinCo]]. Applies to any agent working on the vault (Atlas, Cowork, Claude Code).

## Startup

1. Read `START-HERE.md` first: orientation, canonical register (where everything lives) and invariant rules.
2. Consult the canonical file on the topic before responding. If the information is not in the vault, say so, do not improvise.
3. **Retired repos are not a source of anything**: always use this vault.

## Identity and style

- The identity of the agential CoS (Atlas) is defined in the agent's workspace configuration.
- English, concise, direct, collaborative. Flag risk in one line only when material; no multi-section templates or option dumps.

## Write gates

Single source of truth: `Governance/gates.yml` (summarized in `POL-002` §Zones). Do not redefine it, if there is a discrepancy, `gates.yml` wins.

- **Free (no permission):** `Operations-Samples/`.
- **Gated (Rodney or Alex Carver):** `Governance/SOPs/`, `Agents/`, `Agents/Workflows/`.
- **Blocked (Rodney and Alex Carver):** `Governance/Policies/`.

## How to write to the vault (gated zones → PR)

For any **gated or blocked** change, the agent does NOT push directly to `main`: the gate is a human approval rule. The path is opening a Pull Request via the GitHub API.

- Single file: propose change via PR with the file content
- Multiple files (one branch, one PR): propose all changes in a single PR

Never deliver "pre-filled links" as a substitute: open the actual PR.

For **Free** zones, the agent writes and does `git commit` + `git push` directly to `main`: no PR, no waiting for approval.

## Closing

Local/Cowork sessions with writes end in `git commit` + `git push`. Atlas does the same in Free zones; in Gated/Blocked zones it uses PRs (see above).

> Full details in `START-HERE.md`.

---

*FinCo-OS · AGENTS.md · 2026-07-03*
