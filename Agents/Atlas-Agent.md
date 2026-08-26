---
type: agent
code: AGT-008
title: Atlas Agent
owner: Chief of Staff
status: active
version: 2.0
reviewed: 2026-07-06
review_cycle: quarterly
---

# Atlas-Agent: FinCo's Agenic Chief of Staff

> **v2.0 (2026-06-26):** rewritten to reflect the real architecture. v1.x described custom Python code (`main.py`/`vault.py`/`tools.py`) that **does not exist**: Atlas is Nous Research's open-source framework. See `_Vault/Archive/Atlas-Diagnostico-y-Plan-2026-06-26.md` (archived).

## Identity and mission

Atlas is [[FinCo]]'s agenic Chief of Staff, available to the whole team on Slack as `@finco_cos`. It is the **conversational interface to the FinCo-OS vault**: the team asks, Atlas consults the vault and responds accurately; it also executes and proposes updates.

> It does not replace specialized agents (Ops-Agent, Meeting-Agent). It is the team's conversational access layer.

## What Atlas is (real architecture)

Atlas is a **configuration of the [NousResearch/atlas-agent](https://github.com/nousresearch/atlas-agent) framework** (open-source, MIT), installed on FinCo's VPS. It is not custom code. Its identity and behavior are controlled by **framework configuration files**, not Python:

| Surface | What it controls | Where |
|---|---|---|
| **`SOUL.md`** | Agent identity, layer #1 of the system prompt | `ATLAS_HOME/SOUL.md`, versioned in `_Vault/Atlas/SOUL.md` |
| **`AGENTS.md`** | Working rules on the repo (context file) | Root of FinCo-OS |
| **`config.yaml`** | Model, style, channels, approvals, crons | `ATLAS_HOME/config.yaml` (on VPS) |
| **`memories/`** | Persistent memory (MEMORY.md, USER.md) | `ATLAS_HOME/memories/` |

> Identity is edited in `_Vault/Atlas/SOUL.md` (not in `config.yaml` or code). The old `SYSTEM-PROMPT.md` was **deprecated** (the framework never loaded it) and is archived in `_Vault/Archive/SYSTEM-PROMPT-deprecated-2026-06-26.md`.

## Authority: what it does without asking permission

- Read and search any vault file; answer questions about status, DRIs, decisions, and context.
- Write in **Free** zones (`Meetings/`, `Dev/Backlog/`, `Operations/Cadences/`, `Products/*/Bitacora.md`): minute → commit → push → sync. Without asking permission.
- Refresh the vault from GitHub on request (ff, non-destructive).
- Publish the daily standup in `#finco-dev` (status from Linear, no debtor data).

## Gates: requires approval

| Action | Approver |
|---|---|
| Write in **Gated** zones (`Governance/SOPs/`, `Strategy/Decisions/`, DRI Map, `Agents/`, `Agents/Workflows/`, `Knowledge-Base/`) | Rodney **or** Alex Carver |
| **Blocked** zones (`Governance/Policies/`, `Company/Gates.md`, `Company/Team.md`, OKRs) | Approval + justification |
| Production/runtime/provider mutation; config/secret merge; client commitments; legal assertions | Explicit approval |

Full detail in `SOUL.md` and `START-HERE.md`.

## How to interact (Slack)

```
@finco_cos who is the DRI for Nova?
@finco_cos what is the portfolio status?
@finco_cos update the DRI Map: Nova moved to In Production   (→ gated, asks approval)
@finco_cos refresh from GitHub
@finco_cos generate the weekly executive report   (→ see WF-weekly-executive-report, leaves draft ready and asks approval before publishing in #finco-all)
```

In DMs, no mention needed.

## Infrastructure

| Component | Detail |
|---|---|
| Runtime | Docker containers on Hetzner VPS (`178.156.243.101`), managed by `docker-compose.production.yml` |
| Instances | `default` (Slack/Discord gateway), `dashboard` (web 8787), `kanban-dispatcher` |
| Model | `gpt-5-mini` (OpenAI); delegation `claude-sonnet-4-6` (Anthropic) |
| Vault | `/srv/finco/repos/FinCo-OS` mounted rw; sync with GitHub |
| Platforms | Slack + Discord via gateway (Socket Mode, no public IP) |

## Flow to change Atlas behavior

```
Edit _Vault/Atlas/SOUL.md  (identity)  or  AGENTS.md  (repo rules)
        ↓ commit + push
On the VPS:  bash /srv/finco/runtime/sync-fincoos.sh        # brings change to the repo
        ↓
        bash .../_Vault/Atlas/agent/tools/deploy-soul.sh default   # copies SOUL.md → ATLAS_HOME
        ↓ (--restart for immediate effect)
```

For `config.yaml` changes (style, model, channels): edit on VPS with backup and restart with `down && up` (a `restart` does not apply volumes/env).

## Vault sync

The canonical logic lives in the vault: `_Vault/Atlas/agent/tools/fincoos-sync.sh`.

- `fincoos-sync.sh refresh` → `pull --ff-only`, non-destructive (on-demand / from Atlas).
- `fincoos-sync.sh resync` → `reset --hard origin/main`, authoritative (host cron).

The host wrapper `/srv/finco/runtime/sync-fincoos.sh` only delegates here + does root-only parts (chown, transcripts). `vault-sync.sh` was deprecated (shim → `refresh`).

## References

- `_Vault/Atlas/SOUL.md`: canonical identity.
- `_Vault/Atlas/Atlas.md`: technical reference and operational status.
- `_Vault/Atlas/RUNBOOKS.md`: server commands.
- `_Vault/Archive/Atlas-Diagnostico-y-Plan-2026-06-26.md`: rogue agent diagnosis and remediation plan (archived, 100% executed).
- `Agents/Workflows/WF-weekly-executive-report.md`: weekly executive report (Monday, also on-demand), see `Agents/Ops-Agent.md` for step details.

---

*FinCo-OS · Agents/Atlas-Agent.md · v2.0 · 2026-07-06*
