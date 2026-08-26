---
type: workflow
code: WF-dev-governance
owner: Lead Engineering
agent: Dev-Agent
status: active
version: 1.0
reviewed: 2026-06-12
---

# WF-dev-governance: Development Governance Review

Executed by [[Dev-Agent]]. Trigger: PR opened in GitHub, or when [[Lead Engineering]] / [[Chief of Staff]] request it.

---

## Steps

### 1: PR Check (per new PR)

Verify that the PR meets the 5 rules of SOP-005:

| Rule | Verification |
|---|---|
| **R1** Branch from `dev`, never from `main` | `base_branch == "dev"` |
| **R2** Branch name: `type/PAY-NNN-description` | regex `^(feat\|fix\|chore\|docs)/PAY-\d+-\S+` |
| **R3** PR has link to the Linear issue in the description | search for `PAY-` in the body |
| **R4** Do not merge `dev → main` without [[Lead Engineering]] approval | reviewer required |
| **R5** `main` = production. Only deploy what is in `main` | verify that the deploy targets `main` |

If any rule fails → leave a comment on the PR with the specific rule and corrective action.

### 2: Weekly Scan (Monday)

Review all PRs merged in the previous week:
- Did any violate the rules without being detected?
- Are there orphan branches (`feat/`, `fix/`) unmerged for >7 days?
- Are there Linear issues in "In Progress" without an associated PR for >5 days?

Report findings in `#finco-dev` (draft for approval).

### 3: Incident: incorrect production branch

If a deploy from a branch other than `main` is detected:
1. Notify [[Lead Engineering]] and [[CEO]] immediately
2. Document the incident in `Operations/Incidents/` (create if it doesn't exist)
3. Escalate to [[Chief of Staff]] if no response within 30 minutes

### 4: New dev onboarding

When a new member is added to the development team:
1. Share SOP-005 and ask for read confirmation
2. Add to the `#finco-dev` Slack channel
3. Verify they have correct GitHub access (no admin access if not appropriate)
4. First PR review jointly with [[Lead Engineering]]

---

## Gate: requires approval

| Action | Approver |
|---|---|
| Block a merge | [[Lead Engineering]] |
| Escalate a production incident | [[Chief of Staff]] |
| Publish report in `#finco-dev` | [[Lead Engineering]] |

---

## Output

- Comment on PR (automatic, without approval)
- Weekly report in `#finco-dev` (draft for approval)
- `Operations/Incidents/INC-NNN.md` if there is an incident

---

*FinCo-OS · Agents/Workflows/WF-dev-governance.md · v1.0 · 2026-06-12*
