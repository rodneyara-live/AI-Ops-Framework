---
type: workflow
code: WF-weekly-executive-report
owner: Chief of Staff
agent: Ops-Agent
status: draft
version: 1.3
reviewed: 2026-07-06
---

# WF-weekly-executive-report: Weekly Executive Report

**Executed by:** Ops-Agent (Cowork, on request from Rodney) **or** Atlas (on-demand in Slack, on request from Rodney or anyone)
**Trigger:** on-demand only, **no cron** (Rodney's decision, 2026-07-06, see Changelog v1.3: he evaluated automating it and preferred to request it explicitly each time).
**Scripts:** `weekly-report-data.py`, `weekly-report-render.py`
**Template:** `Governance/Templates/weekly-executive-report-template.md`
**Origin:** explicit request from Rodney on 2026-07-06, after two manual iterations (long version + one-pager) of the week 2026-W27 retrospective. This workflow institutionalizes that process so it no longer depends on improvising the format each time it is requested.

> **Status `draft`:** the content has already been executed end to end once (week 2026-W27, see `Operations/Cadences/Weekly-Review/2026-W27.md` + `2026-W27-onepager.md` + `2026-W27.html`) with Rodney present and approving each design decision in the same session, including a post-first-run correction: the HTML is rendered from the one-pager, not from the long document (see `Governance/Templates/weekly-executive-report-template.md`). Pending to confirm the `files:write` Slack scope and run the on-demand flow a second time (see §Pending) before promoting to `active`. The cron installation **no** longer applies, Rodney decided to keep it on-demand.

---

## Summary

On-demand, when Rodney requests it in Cowork, or someone asks Atlas in Slack (typically on Mondays, but it does not run on its own), an agent (Ops-Agent via Cowork, or Atlas) generates an executive retrospective of the previous week (Monday to Sunday, ISO): meetings, DRI Map progress, development/Linear, knowledge base changes, risks, and commitments. The result is **three files**:

1. **`YYYY-WNN.md`**: long version, mid-management level, the detailed weekly record.
2. **`YYYY-WNN-onepager.md`**: the same structure compressed to one screen.
3. **`YYYY-WNN.html`**: with FinCo's visual identity, **always rendered from the one-pager** (not the long version); this is what is distributed to executives.

Plus a notification message to Rodney for review before publishing in `#finco-all`.

**Level of detail:** mid-management in the long version, executive (one screen) in the one-pager and HTML. See writing rules in the template. Rodney's correction (2026-07-06): the HTML is not the long version "prettified": it is the one-pager with branding. Documenting the long version is fine and should continue (it remains as a record), but it is never the source for the HTML.

---

## Frequency and trigger

No cron, Rodney's decision (2026-07-06): he evaluated automating it with a Monday cron and preferred to keep it on-demand, explicitly requested each time.

| Trigger | Who |
|---|---|
| "Atlas, generate the weekly executive report" (or variants) in Slack, any day | Atlas, on-demand |
| Rodney requests it directly in Cowork | Ops-Agent (Claude) |

## Steps

1. **Calculate the week range**: previous Monday to Sunday from today (most recent fully closed ISO week). E.g., if today is Monday 2026-07-06, the range is 2026-06-29 to 2026-07-05 (`2026-W27`).

2. **Collect data**: run `weekly-report-data.py --week=YYYY-WNN` (see script). Returns a JSON with:
   - `git_log`: vault commits in the range (author, date, message, files)
   - `meetings`: minutes in `Meetings/**` with date in the range (path, type, participants if exposed in frontmatter)
   - `dri_map_snapshot`: full content of `Strategy/DRI-Roadmap-2026/DRI-Map.md` at runtime (agent extracts relevant parts)
   - `linear`: Linear cycle whose range overlaps with the week, scope history, completedIssueCountHistory, and detail of issues with `state=Done` (id, title, assignee, project, priority) via GraphQL
   - `plan_md`: content of `Dev/Cycles/YYYY-WNN/Plan.md` if it exists

3. **Read additional context the script cannot summarize on its own:**
   - Each minute listed in `meetings` (actual decisions and actionables, not the agenda)
   - `Strategy/Decisions/`: new decisions in the range
   - `_Local/Auditoria-Boveda-*.md` and `_Local/Plan-Remediacion-Auditoria-*.md` if there is audit activity that week (the human agent/Atlas running this knows if it applies; if unsure, review `git log` for the range searching for `fix(...)`/`feat(governance)` commits in volume)
   - Previous session memory (if the agent is Claude/Cowork) for sensitive meetings without minutes (e.g., direct reviews with Alex Carver)

4. **Draft the long Markdown** following `Governance/Templates/weekly-executive-report-template.md`: the 7 sections, in that order, without renaming them (the renderer depends on the exact headings). Level of detail: mid-management.

5. **Draft the one-pager**: same 7 sections, compressed to 1-4 lines (or a short table) each. It is not an automatic truncation of the long version; it is a second synthesis from the same knowledge (see template §One-pager structure).

6. **Save both Markdown files** in `Operations/Cadences/Weekly-Review/`: `YYYY-WNN.md` (long) and `YYYY-WNN-onepager.md` (**Free** zone; no gate required for writing, see `Governance/gates.yml`).

7. **Render the branded HTML from the one-pager**: run `weekly-report-render.py Operations/Cadences/Weekly-Review/YYYY-WNN-onepager.md --out=Operations/Cadences/Weekly-Review/YYYY-WNN.html`. Uses the actual identity from `_Vault/DESIGN.md` (Deep Obsidian + Terra Clay + Golden Amber, Inter typography), **not** the palette from `Strategy/DRI-Roadmap-2026/DRI-Map-Avance.html`, which retained placeholder colors (pending someone to fix it separately, outside this workflow's scope). **Never render the HTML from the long `.md`**: it does not fit one screen and that is not the purpose of the HTML.

8. **Commit and push** all three files (long `.md` + `-onepager.md` + `.html`), Free zone, no gate.

9. **Present to Chief of Staff for review**: DM to Rodney (or message in `#agent-approvals` if Atlas) with: the one-pager in text, the link/path to all three files, and the explicit question "should I publish this in `#finco-all`?". **Do not publish without this confirmation**: it is the gate agreed with Rodney on 2026-07-06 (see Company/Gates.md, same pattern as WF-evidence-package).

10. **Upon receiving approval**, publish in `#finco-all` (`C0B30E5EGDU`): the one-pager (text) + the HTML attachment (or its GitHub path if Slack does not allow direct attachment, confirm Atlas' `files:write` scope before the first real delivery).

## Destination

- `Operations/Cadences/Weekly-Review/YYYY-WNN.md`: long version (record)
- `Operations/Cadences/Weekly-Review/YYYY-WNN-onepager.md`: one-pager (HTML source)
- `Operations/Cadences/Weekly-Review/YYYY-WNN.html`: branded HTML, generated from the one-pager

## Gate

| Action | Approver |
|---|---|
| Generate and save the draft (MD + HTML) in `Operations/Cadences/Weekly-Review/` | None, Free zone |
| Publish in `#finco-all` | [[Rodney-Ramirez]] (agreed 2026-07-06) |

If in the future Rodney decides that publication can be automatic (without prior review), update this table and the gate row, do not assume it by default.

## Pending before promoting to `active`

- [ ] Confirm whether Atlas' Slack bot has `files:write` scope to attach the HTML directly, or if it should share a link (e.g., GitHub raw) instead. Instructions: `api.slack.com/apps` → Atlas app → OAuth & Permissions → Bot Token Scopes → add `files:write` if missing → Reinstall to Workspace → if token is rotated, update `SLACK_BOT_TOKEN` in `/srv/finco/secrets/default.env` and `docker compose down && up` (see `_Vault/Atlas/Atlas.md`).
- [ ] Request the on-demand report a second time (week 2026-W28, next Monday 2026-07-13 or when requested) to confirm the flow runs just as well without someone fine-tuning the design in the same session, before removing `status: draft`.

> Installing a cron in `/etc/cron.d/finco` no longer applies, Rodney evaluated it and decided to keep the flow on-demand (see Changelog v1.3). If he changes his mind in the future, reopen this point and also update §Frequency and trigger and §Summary.

## Restrictions

- Never include individual debtor data.
- Never publish in `#finco-all` without explicit approval from Rodney (see §Gate).
- If `weekly-report-data.py` cannot connect to Linear (expired token, network), generate the report anyway with whatever else is available and state it explicitly in section 4, do not block the entire report due to a single down source.
- Files in `Operations/Cadences/` are immutable once committed, correct an error with a new entry or a note, not by editing history.

## Relationship with other workflows

| Workflow | Relationship |
|---|---|
| [[WF-evidence-package]] | Covers progress by DRI (Thursday/Friday); this workflow is the narrative rollup of the entire week (Monday), includes what WF-evidence-package does not cover (meetings, Linear, vault). They do not duplicate; this one can cite the week's Evidence Pack as a source for section 3. |
| [[WF-ops-report]] | Covers daily platform operations (calls, SMS, etc.), different domain (clients/production vs. internal team management). |
| [[WF-weekly-cycle]] | Friday's Check/Act/Plan closes the work cycle; this Monday report narrates what that cycle produced, viewed from the outside (executive). |

## References

- `Governance/Templates/weekly-executive-report-template.md`
- `_Vault/Atlas/agent/tools/weekly-report-data.py`
- `_Vault/Atlas/agent/tools/weekly-report-render.py`
- `_Vault/DESIGN.md`: visual identity
- `Company/Gates.md`: publication gate
- `_Vault/Integrations/Slack-Channels.md`: `#finco-all` channel
- First execution: `Operations/Cadences/Weekly-Review/2026-W27.md`

---

## Changelog

- **v1.3 (2026-07-06, same day):** Rodney evaluated automating the flow with a Monday cron (`0 12 * * 1` UTC) and, after consideration, decided to keep it **on-demand only**: he prefers to request it explicitly each time (in Cowork or to Atlas in Slack) rather than having it run on its own. The cron row is removed from §Frequency and trigger, "install cron" is removed from §Pending, and `Atlas.md`, `RUNBOOKS.md`, and `agent_allowed_scripts.yml` are updated to stop mentioning it as pending installation.
- **v1.2 (2026-07-06, same day):** second correction from Rodney, the HTML used a blue/green/orange/yellow palette inherited from `DRI-Map-Avance.html` that is not FinCo's actual brand. `weekly-report-render.py` now uses the real tokens from `_Vault/DESIGN.md` ("Deep Obsidian" identity: dark header, Terra Clay/Golden Amber accents, Inter typography). Light theme in the body per DESIGN.md §Principles #5 (internal documents).
- **v1.1 (2026-07-06, same day):** correction from Rodney after seeing the first HTML, goes from one artifact (MD → HTML) to three (long MD + one-pager + HTML). The HTML is rendered from the one-pager, not the long version. Also documented that Cowork has no network access to the VPS (verified), installing the cron remains a manual step for Rodney/Alex Carver.
- **v1.0 (2026-07-06):** initial version, executed end to end for 2026-W27.

---

*FinCo-OS · Agents/Workflows/WF-weekly-executive-report.md · v1.3 · 2026-07-06*
