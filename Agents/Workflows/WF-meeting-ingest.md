---
type: workflow
code: WF-meeting-ingest
owner: Chief of Staff
agent: Meeting-Agent
sop: SOP-002
status: active
version: 2.0
reviewed: 2026-07-03
---

# WF-meeting-ingest: Meeting Ingestion

**Executed by:** Meeting-Agent
**Reference:** SOP-002_Meeting-Ingest

> **Channel IDs:** The inline IDs in this workflow are copied from `_Vault/Integrations/Slack-Channels.md`: that is the canonical source. If an ID changes, update there first and then here.

---

## Execution modes

This workflow has two executors. The business rules are identical in both; what differs is the vault writing mechanism.

| Executor | When | Vault write | PRs |
|---|---|---|---|
| `meeting-ingest-worker.py` (cron) | Automatic, 3× per day from Drive | `git commit` direct to `main` | No, Free zone, auto-commit |
| Atlas manual | If cron didn't process, if there is an error, if there is a gate | `vault-propose.sh` → PR → merge | Yes, Chief of Staff review |

The cron is the normal path. The manual path exists as a fallback.

---

## ⚡ Execution mode

**Execute. Do not ask in Slack channels.**

- Run the steps in order without pauses or intermediate confirmations.
- **Prohibited:** writing "shall I proceed?", "do you confirm?", "do you authorize?", "should I do it now?" or any question in any Slack channel.
- **Gates → DM to Chief of Staff** (`U0B5L5Q1VBP`, @rodney). The flow never stops to wait for a response, not even for bilaterals (Step 1b: ignore and notify, without waiting). Other gates generate PR + DM in parallel without stopping the minutes.
- **Technical errors** (Linear 401, permissions, missing file): apply the documented fallback in each step, log in evidence, send DM to Chief of Staff with the error and the action taken. Do not report the error in a public channel. Continue with the next step.
- If the trigger came through a thread in `#finco-ops`, gate or error messages go via **DM**: not in that thread.

---

## Steps

### Step 1: Receive and classify the transcript

**Input:** Google Meet transcript (shared text or Drive link)

1. Identify the meeting date and time
2. Identify participants (list all mentioned)
3. Classify the type:

| Signals | Type | Destination |
|---|---|---|
| Dev team, Tuesday morning, quick checkpoint without demos, 15-20 min | Brief Standup | `Meetings/Team-Syncs/YYYY-MM-DD.md` (only if there are relevant decisions or blockers) |
| Dev team, Thursday morning, demos + progress + decisions, 60 min | Team Sync | `Meetings/Team-Syncs/YYYY-MM-DD.md` |
| Friday afternoon, led by Product/Backlog Owner, plans the cycle with Thursday's digest | Cycle Planning | `Meetings/Cycle-Reviews/YYYY-WNN-Cycle-Planning.md` |
| Team, Friday ~3pm, demo + review + planning | Cycle Review | `Meetings/Cycle-Reviews/YYYY-WNN.md` |
| Chief of Staff + FinCo CEO, structured monthly business review | Operating Review | `Meetings/Operating-Reviews/YYYY-MM.md` |
| Two participants, agenda alignment or ad-hoc topics | Bilateral / 1:1 | None, completely ignored (see Step 1b) |
| External participant to FinCo, demo, onboarding, feedback | Client Meeting | `Meetings/Client-Meetings/[client]-YYYY-MM-DD.md` |
| applicable local debt-collection regulations controls, audit, regulatory incidents | Compliance Meeting | `Meetings/Compliance/YYYY-MM-DD.md` |
| Internal meeting about code, architecture, tools, demos, technical KPIs, infrastructure | Ad-hoc technical | `Meetings/Ad-hoc-Dev/YYYY-MM-DD-[brief-topic].md` |
| Any other internal meeting (processes, reports, administration, clients, finance, compliance) | Ad-hoc operational | `Meetings/Ad-hoc-Ops/YYYY-MM-DD-[brief-topic].md` |

**Operating Review vs Bilateral:** The Operating Review is the formal monthly business review (one per month). The Bilateral is ad-hoc alignment between two people; there can be several in a month. When in doubt, use Bilateral.

---

### Step 1b: Bilateral detected: ignore (if applicable)

**Execute ONLY if the transcript has exactly 2 participants.**

A bilateral **is never processed automatically**: it is private by design. I do not generate minutes, I write nothing to the vault, I do not create issues or add to Backlog-Inbox.

1. Move the transcript to `/srv/finco/transcripts/discarded/YYYY-MM/` (unprocessed).
2. Send an informative DM to Chief of Staff; I do not ask for confirmation, I only notify:

```
🔒 *Meeting-Agent* detected a bilateral meeting and ignored it
*Participants:* [Person1] and [Person2]
*Date:* YYYY-MM-DD

I did not generate minutes nor upload them to the vault, bilaterals are private by design.
The transcript is in discarded/ in case you want to process it manually.
```

3. Continue with the next pending transcript, if any. Do not wait for a response.

> **Rodney's decision (2026-07-03):** if the meeting is bilateral, Atlas ignores it; he decides manually whether to process it or not, outside the automated flow. Previously this step saved a copy in `_Local/Bilaterals/`, but that folder is git-ignored and never exists in the VPS checkout, Atlas couldn't fulfill that instruction anyway.

---

### Step 1c: Vocabulary normalization (automatic, cron only)

> This step is executed by `meeting-ingest-worker.py` transparently, before calling Claude. It does not apply to the manual path (Atlas via Slack).

The worker applies vocabulary corrections defined in `_Vault/Atlas/agent/vocab.json` to compensate for systematic errors in the Google Meet / Gemini transcriber. Examples: "sup a base" → Supabase, "solvecs" → FinCo, "nova cue" → Nova QA.

To add new terms: edit `vocab.json` → commit → push → sync to VPS. See details in `_Vault/Atlas/Atlas.md` (section "Vocabulary normalization").

---

### Step 2: Sanitize

Before writing anything, verify that the transcript does NOT contain:
- Individual debtor data (ID number, name, amount, history)
- Credentials or API keys
- Health information, personal legal information, or other C3 information

If there is sensitive data → clean before processing. If there is C3 information → consult with Chief of Staff before continuing.

---

### Step 3: Generate the minutes

Create the file in the correct folder with this format:

```markdown
# Minutes: [Type], YYYY-MM-DD

**Type:** Brief Standup | Team Sync | Cycle Planning | Cycle Review | Operating Review | Client Meeting | Compliance Meeting | Ad-hoc technical | Ad-hoc operational

> Bilateral does not appear here; it stops at Step 1b, never reaches minute generation.
**Participants:** [[Name1]], [[Name2]]
**Facilitated by:** [[Name]]

## Context
[One sentence about the purpose]

## Decisions made
- [Decision], [[Name]]

## Commitments and actionables
- [ ] [[Name]]: Verb object context 📅 YYYY-MM-DD

> **Date rule for actionables:** If the transcript does not have an explicit committed date for an actionable, use the **Friday of the next cycle close** as the default date (FinCo cycles = 1 week, closes on Friday).
> Example: meeting on Monday 2026-06-22 (W26) → default date `2026-07-03` (Friday W27).
> Never leave an actionable without a date, the next cycle's date is preferable to `null` or "TBD".

## Blockers identified
- [Blocker], DRI: [[Name]]

## Next meeting
[If applicable]
```

---

### Step 4: Classify and route actionables

For each actionable identified in the minutes, determine whether it is **OPS** or **PROD** using these criteria:

**OPS/Admin**: any of these signals:
- Infrastructure task, tools, access, credentials, permissions
- Internal process task: documentation, configuration, reports, monitoring, operational compliance
- Typical DRI: Chief of Staff, Director of Operations, Operations Manager, developer in charge
- Does not modify or add client-visible product behavior

**PROD**: any of these signals:
- Modifies or adds functionality in Conductor, Nova, Creditor Portal, or Strategy Engine
- Architecture task, flow design, feature backlog, UX, agent logic
- Typical DRI: developer in charge, Product/Backlog Owner, Lead Engineering
- Has direct impact on what the client or creditor experiences

**Action by type:**

**If OPS:** add entry to `Operations/Ops-Backlog-Inbox.md`; **do not create an issue in Linear directly**. Chief of Staff reviews, deduplicates against existing issues, and approves before creating in Linear. This avoids duplicates when the worker processes the same meeting more than once.

**If PROD:** add entry to `Dev/Backlog-Inbox.md` with the file's standard format. Do not create an issue in Linear; that requires the Product Owner's gate in the Friday digest.

> If an actionable is ambiguous, classify as PROD and add to Backlog-Inbox. It is better for the Product Owner to discard an item than to miss a relevant one.

**Implementation, canonical block (copy and execute verbatim):**

Replace `[Meeting type] YYYY-MM-DD` with the actual source. The block reads the token, combines the action JSONs, calls the script, and cleans up the temp file. Do not modify paths or variables.

```bash
LINEAR_TOKEN=$(grep '^LINEAR_TOKEN=' /srv/finco/secrets/default.env | cut -d'"' -f2)

python3 - <<'PY'
import glob, json
files = glob.glob('/srv/finco/repos/FinCo-OS/Meetings/_actions/*.json')
files = [f for f in files if 'summary_' not in f]
actions = [json.load(open(f)) for f in sorted(files)]
json.dump(actions, open('/tmp/atlas-actions.json', 'w'))
print(f"Combined: {len(actions)} actions")
PY

python3 /srv/finco/repos/FinCo-OS/_Vault/Atlas/agent/tools/create_issues_from_meeting.py \
  --actions /tmp/atlas-actions.json \
  --linear-token "$LINEAR_TOKEN" \
  --vault-path /srv/finco/repos/FinCo-OS \
  --meeting-source "[Meeting type] YYYY-MM-DD"

rm -f /tmp/atlas-actions.json
```

The script classifies, creates OPS issues in Linear, and adds PROD items to Backlog-Inbox. The audit summary is left in `Meetings/_actions/summary_YYYY-MM-DD.json`.

**If the script returns error 401 / auth failure:**

Do not stop the flow. The fallback JSONs in `Meetings/_actions/` are already ready. Send a DM to Chief of Staff (`U0B5L5Q1VBP`) with exactly this message and continue with Step 5:

```
⚠️ Linear API returned 401 while processing [meeting type] [date].
Actionables saved in Meetings/_actions/; no issues were created in Linear.
Verify or rotate the token in /srv/finco/secrets/default.env and run the script manually when ready.
```

**Do not publish this error in any Slack channel.**

---

### Step 5: Identify DRI Map impacts and log in inbox

For each decision and commitment identified, verify:
- Did any DRI Map component change status?
- Does any DRI have a new next action with a date?
- Is there a new blocker affecting a component?

**If there are changes:** add an entry to `Strategy/DRI-Roadmap-2026/DRI-Map-Backlog-Inbox.md` (via `git commit` direct if the executor is the cron; via PR in the minutes commit if it is manual Atlas). **Do not create a separate PR, do not send a DM to Chief of Staff.** The inbox is the communication channel.

Entry format:

```markdown
### [DRI] [Component]: [Change type], YYYY-MM-DD
**Source:** [Meeting type] YYYY-MM-DD
**Component:** [DRI Map component name]
**Proposed change:** [Concrete description, new status, assigned DRI, next action, blocker]
**Evidence in minutes:** "[quote or paraphrase of the transcript justifying the change]"
**Priority:** High | Medium | Low
```

Mention in the `#agent-approvals` audit (Step 6) how many entries were added to the DRI Map Inbox, without detailing the content.

**If there are no changes:** continue without action.

---

### Step 6: Publish audit in `#agent-approvals` and proceed

Publish in **`#agent-approvals`** (`C0B30JKM0TF`), operational receipt of what Atlas did. **Never to `#finco-ops` or `#finco-dev`**: those channels are for curated content, not agent notifications.

```
📋 *Meeting-Agent* processed the meeting [Type], [YYYY-MM-DD]

*Participants:* [list]
*Actionables:* [N] | OPS→Ops-Inbox: [N] | PROD→Backlog: [N] | DRI Map→DRI-Inbox: [N]

Minutes in vault: `[vault_file_path]` ✅
```

**Do not wait for a response.** Proceed directly to Step 7.

Changes to the DRI Map do require explicit confirmation from the Chief of Staff before opening the corresponding PR (Gated zone).

---

### Step 7: Write to the vault

> **If the executor is `meeting-ingest-worker.py` (cron):** this step is handled by internal `git_push()`: direct commit to `main`, no PR. The minutes, `Dev/Backlog-Inbox.md`, and JSONs from `Meetings/_actions/` are committed in a single push. Proceed to Step 8.

> **If the executor is manual Atlas:** execute `WF-vault-write-github` for each file. Always invoke with `bash` (not `./`):

**7a. Meeting minutes**

```bash
CONTENT_FILE=$(mktemp /tmp/atlas-minuta-XXXXXX.md)
cat > "$CONTENT_FILE" << 'MINUTES'
[content of the minutes generated in Step 3]
MINUTES

bash /srv/finco/repos/FinCo-OS/_Vault/Atlas/agent/tools/vault-propose.sh \
  "Meetings/[subfolder]/YYYY-MM-DD.md" \
  "$CONTENT_FILE" \
  "atlas/meeting-agent/YYYY-MM-DD-[type-slug]" \
  "feat(meetings): Minutes [Type] YYYY-MM-DD" \
  "Minutes processed by Meeting-Agent. Sanitized. Review actionables and decisions before approving."

rm "$CONTENT_FILE"
```

**7b. DRI Map changes** (if any, already logged in `DRI-Map-Backlog-Inbox.md` in Step 5)

DRI Map changes do NOT generate a separate PR or DM to Chief of Staff. They were already deposited in `Strategy/DRI-Roadmap-2026/DRI-Map-Backlog-Inbox.md` in Step 5. Chief of Staff reviews and applies them from that inbox when appropriate.

Capture `PR_URL` of the minutes and notify Chief of Staff only if there are minutes (Free zone does not generate a PR notification):

```
✅ PR opened for your review:

📄 Minutes: [PR_URL_minutes]

Merge what is correct. If something needs adjustment, leave a comment on the PR.
```

> If there are items in the DRI Map Inbox, the `#agent-approvals` audit (Step 6) already mentions them. No additional DM is needed.

---

### Step 8: Recording impact decisions

If there are strategic decisions that require an entry in `Strategy/Decisions/`:
1. Prepare a draft `DEC-NNN_[title].md` with the governance format
2. Open a separate PR with title `feat(decisions): DEC-NNN [title]`
3. Notify Chief of Staff; this PR requires explicit approval

---

### Step 9: Transcript hygiene

**Directory structure:**

```
/srv/finco/transcripts/
├── pending/        ← script places files here upon detection
├── archived/
│   └── YYYY-MM/   ← processed correctly
└── failed/        ← requires human intervention
```

**When starting processing** (happy path), move from `pending/` to `archived/`:

```bash
TRANSCRIPT_FILE="/srv/finco/transcripts/pending/[filename].txt"
ARCHIVE_DIR="/srv/finco/transcripts/archived/$(date +%Y-%m)"
mkdir -p "$ARCHIVE_DIR"
mv "$TRANSCRIPT_FILE" "$ARCHIVE_DIR/"
```

Do this **at the start of Step 3**, not at the end; it serves as a visual lock. If the file is not in `pending/`, it is in flight or already processed.

**In case of failure**, move to `failed/` and notify:

```bash
mkdir -p /srv/finco/transcripts/failed/
mv "$TRANSCRIPT_FILE" /srv/finco/transcripts/failed/
```

Causes that should move to `failed/`:
- Empty or unreadable file
- Individual debtor PII that cannot be sanitized without human context
- Meeting type impossible to classify after two attempts
- `vault-propose.sh` fails with a non-recoverable error

After moving to `failed/`, notify in `#finco-ops` (ID: C0B6MHPTM5K):

```
⚠️ *Meeting-Agent* could not process `[filename]`
*Reason:* [brief description]
*Action required:* [what Chief of Staff needs to do]
File moved to `/srv/finco/transcripts/failed/`
```

A file in `failed/` unattended for more than 24h must be manually reviewed by Chief of Staff.

---

### Step 10: Sync the vault post-merge

After Chief of Staff merges the PRs, sync the vault so the context is updated:

```bash
bash /srv/finco/repos/FinCo-OS/_Vault/Atlas/agent/tools/vault-sync.sh
```

Confirm the latest commit in response to the Slack thread where the merge was notified.

---

### Step 11: Distribute summary in Slack

After merge and sync, publish the meeting summary in the corresponding channel based on type:

> Canonical channel IDs in `_Vault/Integrations/Slack-Channels.md`.

| Type | Destination |
|---|---|
| Brief Standup | `#finco-dev` |
| Team Sync | `#finco-dev` |
| Cycle Planning | `#finco-dev` |
| Cycle Review | `#finco-all` |
| Operating Review | `#finco-ops` |
| Client Meeting | `#finco-gtm` + DM to internal participants |
| Compliance Meeting | `#finco-compliance` |
| Ad-hoc technical (code, architecture, tools, demos, technical KPIs, infrastructure) | `#finco-dev` (C0B30EQLACV) |
| Ad-hoc operational (processes, reports, administration, clients, finance) | `#finco-ops` (C0B6MHPTM5K) |

**Message format, exactly this, nothing more:**

```

📋 *[Meeting type]*, YYYY-MM-DD
*Participants:* Name1, Name2

*Decisions:*
• [Decision made], [Name]

*Actionables:*
• [Name]: brief description 📅 YYYY-MM-DD

*Blockers:* [description], DRI: [Name]   ← omit if no blockers
```

**❌ NEVER include in this message:**
- File paths (`/srv/finco/...`)
- PR number or GitHub links
- Vault sync status or audit log
- Description of what Atlas did (archived, processed, synced)
- Follow-up questions to the team ("Should I do it now?", "Do you confirm?")
- Atlas' own recommendations outside the literal meeting content

> The operational log (PR created, transcript archived, vault synced) goes **only to `#finco-ops` (ID: C0B6MHPTM5K)** in steps 5 and 6. It should never appear in the team's distribution channel.

**Rules:**
- The message is meeting content, not an execution report from Atlas
- For DMs: send the same message to each participant individually
- For `#finco-all`: add a cycle close context line if applicable
- Never include debtor data or C3 information

---

## Quality checklist

Before considering the ingestion complete:

- [ ] Minutes have correct date, type, participants
- [ ] Zero individual debtor data
- [ ] All actionables have an owner (WikiLink) and date (if no explicit date → Friday of next cycle)
- [ ] Each actionable classified as OPS or PROD (none left unclassified)
- [ ] OPS items added to `Operations/Ops-Backlog-Inbox.md` (pending manual review)
- [ ] PROD items added to `Dev/Backlog-Inbox.md`
- [ ] Minutes PR created and URL sent to Chief of Staff
- [ ] DRI Map changes added to `DRI-Map-Backlog-Inbox.md` (if any); no separate PR or DM
- [ ] Strategic impact decisions identified and separate PR opened if applicable
- [ ] vault-sync.sh executed after merge
- [ ] Summary distributed in the Slack channel corresponding to the meeting type

---

## References

- `Agents/Workflows/WF-vault-write-github.md`: writing mechanism via PR
- `_Vault/Atlas/agent/tools/vault-propose.sh`: proposal script via GitHub API
- `_Vault/Atlas/agent/tools/vault-sync.sh`: post-merge sync script
- `Governance/SOPs/SOP-002_Meeting-Ingest.md`: SOP-level process
- `Agents/Meeting-Agent.md`: agent authority and gates

---

## Changelog

- **v2.0 (2026-07-03):** Plan B #10, bilaterals are no longer saved anywhere (not even `_Local/Bilaterals/`, which never existed in the VPS checkout anyway). Atlas now ignores them completely and only notifies via DM, without waiting for a response or offering A/B options. Rodney's decision: he decides manually whether to process a bilateral, outside the automated flow.
- **v1.9 (2026-07-03):** corrects C5 from the 2026-07-03 audit, Step 1 table aligned with `Meeting-Agent.md` (adds Brief Standup and Cycle Planning, corrects Team Sync to Thursday); Bilateral corrected to `_Local/Bilaterals/` (contradicted its own Step 1b); removes the phantom path `Operations/_Local/`; Step 3 and Step 11 with the complete list of 10 types.
- **v1.8 (2026-06-25):** previous version.

---

*FinCo-OS · Agents/Workflows/WF-meeting-ingest.md · v2.0 · 2026-07-03*
