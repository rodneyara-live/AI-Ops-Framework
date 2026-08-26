---
type: sop
code: SOP-005
title: Development Governance
owner: Lead Engineering
status: active
version: 1.4
reviewed: 2026-06-23
review_cycle: on-change
supersedes: null
---

# SOP-005: Development Governance

## Purpose

Establish the rules, conventions, and change control protocol governing software development at FinCo, ensuring full traceability, verifiable quality, and production environment stability.

## Scope

Applies to the entire development team across all active FinCo repositories, without exception for urgency or hierarchy. Technical authority over compliance with this SOP rests with the Lead Engineering. The Dev-Agent runs continuous automated surveillance.

> The role-to-person mapping is in `Company/Team.md`.

## Fundamental rule

No code reaches `main` without a prior issue, a working branch, an approved PR, and review from the Lead Engineering. This applies without exception, including urgent fixes and minor changes.

---

## The 8 Rules

### Rule 1: Issue before code

Every task, fix, or new feature requires a registered issue before writing any line of code. The issue can live in Linear or GitHub, what matters is that the record exists.

**Mandatory chain:** decision → issue → branch → PR → merge.

No issue means no branch. No branch means no PR. No PR means no production.

**Granularity criteria:** do not create an issue for every minor atomic action. Spelling corrections, non-functional style adjustments, and trivial changes are grouped into a single commit without a separate issue. Related tasks within the same phase or module go into a single issue; GitHub provides Labels and Milestones for grouping. The tool must serve the team, not the other way around.

### Rule 2: Branch naming convention

| Branch | Purpose | Access |
|---|---|---|
| `main` | Production. Only via approved PR. | Merge only, never direct push |
| `develop` / `unified` | Integration and staging | Development team |
| `feat/PAY-NNN-description` | New feature | Dev assigned to the issue |
| `fix/PAY-NNN-description` | Bug fix | Dev assigned to the issue |
| `chore/PAY-NNN-description` | Maintenance, documentation, refactor | Dev assigned to the issue |

The branch name must include the Linear issue identifier (`PAY-NNN`) to ensure direct traceability between code and task.

### Rule 3: Merge control to main: approval and execution

Merges to `main` have two separate controls:

**3.1, Approval (who must review and approve the PR)**

Every PR to `main` requires explicit approval from the Lead Engineering before merge. The reviewer must verify:

- The change does what the issue and PR description state
- It does not introduce regressions in existing functionality
- The PR description includes: what changed, why, and how to test

**3.2, Execution (who can click "Merge")**

Only two roles can execute the merge once approved:

- The **Lead Engineering**, or
- The **technical DRI of the affected product**, according to the corresponding component in `Strategy/DRI-Roadmap-2026/DRI-Map.md`

No other person may execute the merge, even if the PR is approved. This control must be enforced at the GitHub level via branch protection rules (restriction on who can push or merge to `main`).

**3.3, Exception protocol**

If neither of the two authorized persons is available and the change is urgent (production incident):

1. The dev notifies the situation and the PR in question in `#finco-dev`.
2. Obtains written authorization from the **CEO** (gate approver) in `#agent-approvals` (see `Company/Gates.md`).
3. Any team member with access may execute the merge under that authorization.
4. The dev logs the exception in `/docs/cutovers/` with the field `exception: true` and reference to the CEO's message.

### Rule 4: Cutover log

Every environment change, project migration, critical variable modification, or branch/service deprecation is logged in `/docs/cutovers/YYYY-MM-DD_description.md` with the following format:

```markdown
## Cutover: [brief description]
**Date:** YYYY-MM-DD
**From:** [origin, project / branch / URL]
**To:** [destination, project / branch / URL]
**Affected services:** [list]
**Deprecated:** [list]
**Pending:** [unresolved items]
**Author:** @handle
```

### Rule 5: Live CLAUDE.md per repository

Each active repository maintains an updated `CLAUDE.md` with:

- **Active Supabase:** project URL (without credentials)
- **Production branch:** which is `main` at this moment
- **Environment per service:** what the frontend, backend, and workers use
- **Deprecated:** projects or branches that should no longer be touched
- **Environment variables:** names (without values)

Update responsibility: the Lead Engineering and the Dev responsible for the repository, at every cutover and every environment change.

### Rule 6: GitHub ↔ Linear synchronization

Issues may originate in either system, but synchronization is asymmetric:

- **Linear → GitHub (mandatory):** every technical issue logged in Linear must also exist in GitHub. This ensures branch traceability and context for the assistant agent.
- **GitHub → Linear (human decision):** not everything on GitHub goes up to Linear. If a GitHub issue is relevant to the wider team or to cycle planning, the dev manually creates it in Linear. There is no automation for this cross-reference for now, selection is the assigned dev's responsibility.

The goal is for Linear not to become a repository of technical noise, and for GitHub to always have the complete record of what is being worked on.

### Rule 7: Standard `/docs/` structure

Every active FinCo repository starts with the following base folders inside `/docs/`:

| Folder | Purpose |
|---|---|
| `security/` | Access policies, secrets management, security notes |
| `business/` | Business rules, flows, product and client context |
| `architecture/` | Architecture decisions, diagrams, system structure |
| `brand/` | Brand assets and guidelines (until a centralized official source exists) |
| `legacy/` | Documentation that is no longer current but worth keeping for reference |
| `future/` | Ideas, planned features, technical roadmap notes |

This structure is a **starting point, not a straitjacket**. Each project may add folders according to its particular history. Folders like `unified/` or `recovery/` are specific to particular contexts and are not part of the base standard.

Responsibility: the dev who creates the repository initializes this structure. The Lead Engineering verifies it in the first PR.

### Rule 8: Vault for assistant agents in development

Every assistant agent (Claude Code or other) operating on a FinCo repository requires an active vault (`_vault/`) with the project context. Working without a vault means starting each session from scratch; no decision history, open bugs, or project status.

**Mandatory:**
- The repository must have an operational `_vault/` before starting work with an agent. If it does not exist, the agent creates it on bootstrap at the start of the first session.
- Each member's personal vault must include **FinCo-OS cloned as a read-only folder**. When starting a session, updating the clone (`git pull`) ensures the agent works with the current version of policies, branding, and corporate structure.

**Recommended:**
- Use the vault skeleton documented in `Governance/Guides/Dev-Agents-Guide.md` as a starting point. The internal structure of the personal vault is a territory of individual autonomy, each member adapts it to their way of working.

Technical spec and installation instructions: `Governance/Guides/Dev-Agents-Guide.md`.

---

## Production change protocol

Follow in this order. Do not skip steps.

1. Create an issue in Linear or GitHub describing the change and its justification.
2. Create a working branch from `develop`/`unified`. Never from `main`.
3. Verify the proposed solution with the assistant agent before implementing. This step reduces approach errors before investing time in code.
4. Develop and verify locally. Ensure thorough testing before moving forward.
5. Open a PR to `develop`/`unified` with complete description.
6. Notify in `#finco-dev`: what is being merged and what it affects.
7. Verify in staging that the behavior is as expected.
8. Open a PR to `main` and assign to the Lead Engineering for review.
9. Post-merge: verify in production immediately and confirm in `#finco-dev`. Close the issue in **both systems** (Linear and GitHub).
10. For any post-deploy anomaly: rollback first, diagnosis second.

---

## Architecture principles

- Do not modify functionality in production without a branch, PR, and prior notice.
- Prefer creating new over modifying existing, new tables, functions, and flows minimize regression risk.
- System administration functions go in separate projects, not in the main platform.

---

## Quality metrics

This SOP is being executed correctly when:

- 100% of merges to `main` have a PR with Lead Engineering review.
- There are no branches older than 7 days without activity and without a registered justification.
- Every environment change has its entry in `/docs/cutovers/`.
- The Dev-Agent does not report unattended violations in `#finco-dev`.

---

## Violation protocol

1. The Lead Engineering notifies the involved dev directly.
2. The problem is fixed before resuming any new task.
3. In case of recurrence by the same dev, the Ops Lead joins the conversation.
4. The Dev-Agent records the pattern in the `#agent-audit-log` audit log.

---

## Executed by

Dev-Agent (`Agents/Dev-Agent.md`). See `Agents/Workflows/WF-dev-governance.md` for detailed steps.

## Review

Reviewed when a production incident reveals a gap not covered by this SOP, or when the team proposes a documented improvement. Owner: Lead Engineering, with Ops Lead approval.

---

*FinCo-OS · SOP-005 · v1.4 · 2026-06-23*
