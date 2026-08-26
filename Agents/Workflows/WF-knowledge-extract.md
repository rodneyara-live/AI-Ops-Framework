---
type: workflow
code: WF-knowledge-extract
owner: Chief of Staff
agent: Knowledge-Agent
status: active
version: 1.1
reviewed: 2026-07-02
---

# WF-knowledge-extract: On-demand knowledge distillation

**Executed by:** Knowledge-Agent (invoked by Atlas via Slack, manual; no cron)
**Reference:** `Agents/Knowledge-Agent.md`

---

## Summary

The Chief of Staff identifies that a meeting, transcript, or document contains valuable operational or product knowledge (processes, criteria, architecture decisions) that would be lost if not documented. Instead of processing it himself, he tells Atlas via Slack and Atlas runs this workflow: obtains the raw source, distills it into Knowledge-Base entries (and SOP drafts if applicable), and proposes them via PR for approval.

This is different from `WF-meeting-ingest`: Meeting-Agent generates minutes (decisions/actionables/blockers) for **every** meeting, automatically via cron. Knowledge-Agent distills **reusable knowledge** (the "how" and the "why") and only runs **on-demand**, when the Chief of Staff explicitly asks for it. Both can process the same meeting; they are not mutually exclusive.

---

## Execution mode

**Run the extraction immediately. Do not ask "shall I proceed?" before working on the draft.**

- The only approval point is the final PR to `Knowledge-Base/`: because it is a Gated zone (`_Vault/Atlas/SOUL.md`, `Company/Gates.md` § "Promote to Knowledge Base"). That is not a blocking question mid-flow: Atlas does all the distillation work and arrives with a draft ready to approve or adjust.
- Gates → DM to Chief of Staff (`U0B5L5Q1VBP`, @rodney). Never in a public channel.
- If the source is ambiguous or incomplete, flag the gap explicitly in the draft ("Pending gaps" section) instead of inventing content or stopping the flow to ask.

---

## Steps

### Step 1: Receive instruction and obtain the source

**Accepted input (any of these, as shared by Chief of Staff):**

| Source form | How to obtain |
|---|---|
| Link to a Google Doc in Drive (Gemini transcript, with Notes/Transcript tabs) | Same mechanism as `drive-transcript-watch.py`: Docs API with `includeTabsContent=true`. Extract both tabs; use TRANSCRIPT as primary source, NOTES as additional context. |
| Text pasted directly in Slack | Use it as-is as source. No download required. |
| Reference to something already in the vault (minutes in `Meetings/`, an existing document in `Knowledge-Base/` to enrich) | Read the file directly from the vault. |

Identify: date, participants, topic/purpose of the session.

> **Never operate on the already-processed minutes as the sole source if the raw transcript is available**: the minutes summarize decisions/actionables but discard the reasoning and technical context that is precisely what this workflow seeks to capture. If only the minutes exist (transcript no longer available), use them and flag it as a limitation in "Pending gaps".

---

### Step 2: Sanitize

Before distilling anything, verify that the source does NOT contain:
- Individual debtor data (ID number, name, amount, history)
- Credentials, tokens, or API keys
- C3 information (maximum confidentiality, health, personal legal)

If there is sensitive data → exclude it from the distilled content. If there is C3 information → consult with Chief of Staff before continuing.

---

### Step 3: Identify knowledge type(s)

A single meeting can produce multiple entries in different categories. Classify each piece of identified knowledge according to the table in `Agents/Knowledge-Agent.md`:

| Knowledge type | Destination |
|---|---|
| Negotiation scripts, escalation criteria, contact timings | `Knowledge-Base/Collections/` |
| Extrajudicial processes, legal framework | `Knowledge-Base/Legal/` |
| Technical configuration (telephony, N8N, Supabase, monitoring) | `Knowledge-Base/Infrastructure/` |
| Compliance rules, approved templates | `Knowledge-Base/Compliance/` |
| Product design/architecture (feature discovery, engines, design decisions and discarded alternatives) | `Knowledge-Base/Product/` |
| Communication tone/terminology by strategy decision, not by law | `Knowledge-Base/Communication/` |

If a described process is repeatable and does not have an SOP → also mark it as a candidate for an SOP draft (Step 6).

---

### Step 4: Structure each entry

Use the standard format from `Agents/Knowledge-Agent.md`:

```markdown
# [Knowledge title]

**Source:** [[Expert-Name]]: session YYYY-MM-DD
**Area:** Collections | Legal | Infrastructure | Compliance | Product
**Validated:** No, pending review by [[Chief-of-Staff]]

## Description
[One sentence about what this knowledge is and when it applies]

## Rule or process
[The knowledge in clear, actionable format. For Product: include decision made, discarded alternatives, and the rationale.]

## Exceptions or special conditions
[Edge cases or conditions that change the rule]

## Pending gaps
[What was not clear, questions for the next session]
```

If the session produces multiple entries (e.g., a meeting touches on monitoring AND escalation criteria), generate one file per entry, do not mix topics in a single document.

---

### Step 5: Flag gaps

If something in the source was ambiguous, contradictory, or incomplete, document it explicitly in "Pending gaps" of the file AND mention it in the DM message of Step 8. Do not improvise content to fill the void.

---

### Step 6: Evaluate SOP candidacy

If any piece of knowledge describes a repeatable process (not a one-off rule), additionally prepare an SOP draft in `Governance/SOPs/` using `Governance/Templates/sop-template.md`, with status `draft`, owner = DRI of the process (see `Company/Team.md`).

---

### Step 6b: Quality check before PR (mandatory)

**Incident 2026-07-02:** out of 6 entries generated in the first run of this workflow, 2 arrived with corrupted accented characters (NUL bytes followed by hex text) and had to be reconstructed manually after the merge. Before writing the temp file in Step 7, re-read the entire generated content and verify:

- [ ] No word with accented characters shows a broken pattern (fragments like "f3", "e9", "e1", "f1", "c1" stuck to a word, non-printable characters, or text that clearly lost a letter). If it appears, regenerate the content, do not propose the PR with the corruption present.
- [ ] Each person in `Source` and `Validated` is an individual WikiLink (`[[Firstname-Lastname]]`, one per person, never multiple names within a single `[[...]]`), using the exact name from `Company/Team.md`. If the person does not have a page in the vault, plain text without brackets.
- [ ] `Validated` references `[[Chief-of-Staff]]` (role), not a specific person, unless already validated.
- [ ] `Area` uses a single category from the Step 3 table (or multiple separated by `|` only if it genuinely applies to both, do not use Compliance just because the topic is sensitive; see Communication vs. Compliance distinction in `Agents/Knowledge-Agent.md`).

---

### Step 7: Write via PR (Gated, never auto-commit)

`Knowledge-Base/` is a Gated zone. Same as Meeting-Agent with DRI Map or strategic decisions, this **always** goes via PR, never automatic `vault-merge.sh`.

```bash
CONTENT_FILE=$(mktemp /tmp/atlas-knowledge-XXXXXX.md)
cat > "$CONTENT_FILE" << 'KNOWLEDGE'
[content of the entry generated in Step 4]
KNOWLEDGE

bash /srv/finco/repos/FinCo-OS/_Vault/Atlas/agent/tools/vault-propose.sh \
  "Knowledge-Base/[Area]/[Title-slug].md" \
  "$CONTENT_FILE" \
  "atlas/knowledge-agent/YYYY-MM-DD-[title-slug]" \
  "feat(knowledge): [Area] — [brief title]" \
  "Distilled by Knowledge-Agent from [source type]. Unvalidated — requires Chief of Staff review before merge."

rm "$CONTENT_FILE"
```

Repeat for each entry generated (one PR per file, or one PR grouping all entries from the same session, prefer a single PR per distillation session to facilitate review).

If an SOP draft was also generated (Step 6), include it in the same PR or a separate one, clearly indicating it in the PR message.

---

### Step 8: Notify Chief of Staff via DM

**Never in a public channel**: unlike Meeting-Agent minutes, this content is not distributed to the team until it is validated.

```
📚 *Knowledge-Agent* distilled [topic/meeting], YYYY-MM-DD

*Proposed entries:* [N] → [list of areas: Product, Infrastructure, ...]
*SOP candidate:* [Yes/No, which one]
*Gaps identified:* [brief list, or "none"]

PR for your review: [PR_URL]

Merge what is correct. Adjust or discard what is not.
```

---

### Step 9: Source hygiene

- If the source was a Drive link: do not download a persistent copy beyond what is strictly necessary for processing. If a temporary file was generated, delete it when done (`rm`).
- If the source was text pasted in Slack: do not rewrite it in any vault file. Only the distilled knowledge is persisted.
- **The raw transcript never enters the vault**, same as in `WF-meeting-ingest`.

---

### Step 10: Sync after merge

After Chief of Staff merges the PR:

```bash
bash /srv/finco/repos/FinCo-OS/_Vault/Atlas/agent/tools/vault-sync.sh
```

Confirm the final commit in the Slack thread where the merge was notified.

---

## Quality checklist

- [ ] Source identified and sanitized (no debtor data, credentials, C3)
- [ ] Each piece of knowledge classified in its area (Collections/Legal/Infrastructure/Compliance/Product/Communication)
- [ ] Standard Knowledge-Agent format respected in each entry
- [ ] No encoding corruption in accented characters (see Step 6b) and individual WikiLinks per person
- [ ] Pending gaps explicitly flagged, not improvised
- [ ] SOP candidate evaluated (created if applicable)
- [ ] PR opened, never auto-commit to `Knowledge-Base/`
- [ ] DM sent to Chief of Staff, never published in a public channel
- [ ] Raw transcript not persisted in the vault or on disk after processing
- [ ] `vault-sync.sh` executed post-merge

---

## References

- `Agents/Knowledge-Agent.md`: agent identity, authority, and gates
- `Agents/Workflows/WF-meeting-ingest.md`: analogous workflow for minutes (automatic, Free zone)
- `Agents/Workflows/WF-google-drive-transcript-watch.md`: mechanism for extracting Notes/Transcript tabs from Drive
- `_Vault/Atlas/agent/tools/vault-propose.sh`: PR creation
- `_Vault/Atlas/agent/tools/vault-sync.sh`: post-merge sync
- `Company/Gates.md` § "Promote to Knowledge Base"
- `_Vault/Atlas/SOUL.md`: Free/Gated/Blocked zones

---

*FinCo-OS · Agents/Workflows/WF-knowledge-extract.md · v1.1 · 2026-07-02*
