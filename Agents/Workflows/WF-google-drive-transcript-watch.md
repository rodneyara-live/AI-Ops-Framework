---
type: workflow
code: WF-google-drive-transcript-watch
owner: Chief of Staff
agent: Atlas
status: active
version: 2.0
reviewed: 2026-06-22
---

# WF-google-drive-transcript-watch: Automatic transcript ingestion from Drive

**Executed by:** Two cron scripts on the VPS (no human intervention)
**Complements:** `WF-meeting-ingest.md`

---

## Summary

Two cron scripts process meetings fully autonomously. The first downloads transcripts from Drive; the second processes them by calling the Anthropic API directly and runs WF-meeting-ingest end to end. No intermediate Slack messages, only the final output in the correct channel.

---

## Architecture

```
Google Meet ends
  → Gemini generates transcript → saves in Rodney's Drive
  → Cron 1 (9am / 1pm / 6pm RD), drive-transcript-watch.py
      → Detects new files (createdTime > last_check)
      → Downloads → saves to /srv/finco/transcripts/pending/
      → (silent; no Slack messages)
  → Cron 2 (9:10am / 1:10pm / 6:10pm RD), meeting-ingest-worker.py
      → Reads pending/*.txt
      → Calls Anthropic API directly with the transcript
      → Claude classifies the meeting and generates: minutes + action items + Slack summary
      → Writes minutes to the vault → git push
      → Creates OPS issues in Linear + PROD items in Backlog-Inbox
      → Posts operational log in #finco-ops
      → Posts summary in the channel for the meeting type (#finco-dev / #finco-all / etc.)
      → DM to Rodney ONLY if there are gates: bilateral, DRI Map changes, errors
      → Moves transcript from pending/ to archived/YYYY-MM/
```

---

## Initial setup (one-time): Step by step

### Phase 1: Google Cloud Project

1. Go to [console.cloud.google.com](https://console.cloud.google.com)
2. Create a new project: `finco-atlas`
3. Go to **APIs & Services → Library**
4. Search for and enable: **Google Drive API**
5. Go to **APIs & Services → Credentials**
6. Click **+ Create Credentials → OAuth 2.0 Client IDs**
7. Application type: **Desktop app**
8. Name: `atlas-drive-watcher`
9. Download the JSON → save as `client_secret.json` on your local machine

> ⚠️ Also go to **OAuth consent screen** and add `rodney@finco.io` as a Test User if the project is in "Testing" mode.

---

### Phase 2: Get the refresh token (from your local machine)

```bash
# Install dependency
pip install google-auth-oauthlib

# Run the setup script
python3 _Vault/Atlas/agent/tools/google-auth-setup.py \
  --credentials ~/Downloads/client_secret.json
```

The browser opens → you authorize with `rodney@finco.io` → the script prints:

```
GOOGLE_CLIENT_ID="..."
GOOGLE_CLIENT_SECRET="..."
GOOGLE_REFRESH_TOKEN="..."
```

---

### Phase 3: Configure secrets on the VPS

```bash
ssh -i ~/.ssh/hetzner_finco root@178.156.243.101

# Edit the secrets file
nano /srv/finco/secrets/default.env
```

Add the three lines from the previous step plus the Channel ID for `#agent-approvals`:

```bash
GOOGLE_CLIENT_ID="..."
GOOGLE_CLIENT_SECRET="..."
GOOGLE_REFRESH_TOKEN="..."
SLACK_AGENT_APPROVALS_CHANNEL_ID="C..."   # Channel ID of #agent-approvals — see Slack-Channels.md
```

**Get the Channel ID for `#agent-approvals`:** in Slack, right-click the channel → *View channel details* → at the end of the URL or in the About section.

---

### Phase 4: Install Python dependency on the VPS

```bash
pip install requests --break-system-packages
# (requests is usually already available, verify)
python3 -c "import requests; print('OK')"
```

---

### Phase 5: Mount transcript folder in the Docker container

Edit `/srv/finco/runtime/docker-compose.production.yml` and add the volume mount:

```yaml
volumes:
  - /srv/finco/repos/FinCo-OS:/srv/finco/repos/FinCo-OS:ro
  - /srv/finco/transcripts:/srv/finco/transcripts:ro   # ← add this line
```

Restart the containers:

```bash
cd /srv/finco/runtime
docker compose -f docker-compose.production.yml down && \
docker compose -f docker-compose.production.yml up -d
```

---

### Phase 6: Configure cron on the VPS

```bash
crontab -e
```

Add **both** scripts (the worker runs 10 minutes after the watcher):

```cron
# drive-transcript-watch — 9am, 1pm, 6pm RD time (UTC-4)
0 13 * * * python3 /srv/finco/repos/FinCo-OS/_Vault/Atlas/agent/tools/drive-transcript-watch.py >> /srv/finco/logs/transcript-watch.log 2>&1
0 17 * * * python3 /srv/finco/repos/FinCo-OS/_Vault/Atlas/agent/tools/drive-transcript-watch.py >> /srv/finco/logs/transcript-watch.log 2>&1
0 22 * * * python3 /srv/finco/repos/FinCo-OS/_Vault/Atlas/agent/tools/drive-transcript-watch.py >> /srv/finco/logs/transcript-watch.log 2>&1

# meeting-ingest-worker — 9:10am, 1:10pm, 6:10pm RD time (10 min after watcher)
10 13 * * * python3 /srv/finco/repos/FinCo-OS/_Vault/Atlas/agent/tools/meeting-ingest-worker.py >> /srv/finco/logs/meeting-ingest-worker.log 2>&1
10 17 * * * python3 /srv/finco/repos/FinCo-OS/_Vault/Atlas/agent/tools/meeting-ingest-worker.py >> /srv/finco/logs/meeting-ingest-worker.log 2>&1
10 22 * * * python3 /srv/finco/repos/FinCo-OS/_Vault/Atlas/agent/tools/meeting-ingest-worker.py >> /srv/finco/logs/meeting-ingest-worker.log 2>&1
```

> RD time (UTC-4): 9am = 13:00 UTC · 1pm = 17:00 UTC · 6pm = 22:00 UTC

---

### Phase 7: Smoke test

```bash
# 1. Verify that drive-transcript-watch finds and downloads
python3 /srv/finco/repos/FinCo-OS/_Vault/Atlas/agent/tools/drive-transcript-watch.py --force
tail -20 /srv/finco/logs/transcript-watch.log

# 2. Verify there is something in pending/
ls /srv/finco/transcripts/pending/

# 3. Run the worker manually
python3 /srv/finco/repos/FinCo-OS/_Vault/Atlas/agent/tools/meeting-ingest-worker.py
tail -30 /srv/finco/logs/meeting-ingest-worker.log
```

If it works: the minutes appear in the vault, the summary arrives in the correct channel, and the operational log goes to #finco-ops.

---

## Daily operation

### Automatic trigger (cron)
Both scripts run on their own. No intervention required. Logs:
- `tail -f /srv/finco/logs/transcript-watch.log`
- `tail -f /srv/finco/logs/meeting-ingest-worker.log`

### On-demand trigger (VPS)
To process a meeting outside cron hours:
```bash
# Step 1: download from Drive
python3 /srv/finco/repos/FinCo-OS/_Vault/Atlas/agent/tools/drive-transcript-watch.py --force

# Step 2: process immediately
python3 /srv/finco/repos/FinCo-OS/_Vault/Atlas/agent/tools/meeting-ingest-worker.py
```

### View downloaded / in-process transcripts
```bash
ls /srv/finco/transcripts/pending/
ls /srv/finco/transcripts/archived/
ls /srv/finco/transcripts/failed/
```

### Last check status
```bash
cat /srv/finco/state/last-transcript-check.json
```

---

## Directory structure

```
/srv/finco/transcripts/
├── pending/          ← script places files here upon detection
├── archived/
│   └── YYYY-MM/      ← Atlas moves upon STARTING processing (lock + happy path)
└── failed/           ← Atlas moves if unable to complete without human intervention
```

The `archived/YYYY-MM/` and `failed/` folders are created by Atlas with `mkdir -p`: no need to create them manually.

**Alert signals:**
- File in `pending/` for more than 48h → Atlas didn't see it or failed silently
- File in `failed/` → requires [[Chief of Staff]] attention within 24h

See `WF-meeting-ingest.md` Step 8 for the full detail of each case.

---

## Exported file format

Gemini generates Google Docs with **two tabs**:

| Tab | Name | Content |
|---|---|---|
| Tab 1 | Notes | Structured summary by Gemini |
| Tab 2 | Transcript | Full conversation, with speakers |

The script uses the **Google Docs API** (`includeTabsContent=true`) to extract both tabs separately. The file saved in `/srv/finco/transcripts/pending/` has this structure:

```
=== NOTES (Gemini summary) ===
[Gemini summary]

=== TRANSCRIPT ===
[full conversation with speakers and timestamps]
```

**Atlas must use the TRANSCRIPT section to extract decisions and actionables.** The NOTES section is additional context, Gemini already made a summary but may not capture WikiLinks nor FinCo context.

### Extraction methods (field in the log)

| Method | What it means |
|---|---|
| `tabs_extracted` | ✅ Found and extracted properly named tabs |
| `tabs_all_unrecognized` | ⚠️ Tabs exist but names don't match, save everything, review naming |
| `drive_export_no_tabs` | Document without tabs, standard export OK |
| `drive_export_fallback` | Docs API unavailable, full export without separators |
| `direct_download` | Plain text file (not a Google Doc) |

---

## Troubleshooting

| Symptom | Probable cause | Solution |
|---|---|---|
| Doesn't find transcripts in Drive | Gemini saves them in a different folder | Verify in Drive where files end up; adjust query in script |
| Google 401 error | Refresh token expired or revoked | Repeat Phase 2 to get a new token |
| Duplicate files in pending/ | `processed_ids` corrupted | Delete `/srv/finco/state/last-transcript-check.json` and run watcher with `--force` |
| `tabs_all_unrecognized` method in log | Gemini changed the tab names | Review with `cat`: update `TRANSCRIPT_NAMES` / `NOTES_NAMES` in watcher |
| Exported file empty or incomplete | Docs API couldn't extract the body | Verify in Drive that the file has content; review JSON structure |
| Transcript in `pending/` for more than 2h | meeting-ingest-worker didn't run or failed | Check worker log; run manually |
| Transcript in `failed/` | Bilateral gate, PII C3, or worker error | Review Rodney's DM with the reason; process manually |
| Worker fails with invalid JSON | Claude didn't return pure JSON | Review `tail -30 /srv/finco/logs/meeting-ingest-worker.log`; transcript in `failed/` |
| Linear 401 in worker | Token expired | Rotate token in `default.env`; run `create_issues_from_meeting.py` manually with fallback JSON |

---

## Known limitation: meetings where Rodney is a guest

Gemini creates the "Notes by Gemini" file in the **organizer's** Drive, not in each participant's. The file is shared with all participants (appears in "Shared with me"), and the script already includes `includeItemsFromAllDrives: true` to capture it.

**Manual workaround (until a service account is available):** If the file doesn't automatically appear in the next cron check, move it manually to `Meet Recordings` in My Drive so the next check detects it.

### Technical debt: migration to service account

The current approach uses [[Rodney-Ramirez]]'s personal OAuth2 with a refresh token.
<!-- EXCEPTION DOCUMENTED: reference to a specific person required, the OAuth token is tied to the rodney@finco.io account. See migration plan to service account at the end of this section. --> This has two limitations:

1. The token expires and requires manual re-authentication
2. It only sees Rodney's Drive (though with `includeItemsFromAllDrives` it captures "Shared with me")

**Pending migration:** use a Google **service account** with **domain-wide delegation** over the `@finco.io` domain. This enables:
- Automatic authentication without expiring refresh tokens
- Searching the Drive of any team member
- Capturing meetings where any FinCo member is the organizer

**Migration reference:**
1. Google Cloud Console → IAM → Service Accounts → create `atlas-drive-watcher@finco-atlas.iam.gserviceaccount.com`
2. Enable domain-wide delegation in Google Workspace Admin → Security → API Controls
3. Generate JSON key and save to `/srv/finco/secrets/google-service-account.json`
4. Update `drive-transcript-watch.py` to use `google.oauth2.service_account.Credentials` with `subject=rodney@finco.io`

---

## References

- `_Vault/Atlas/agent/tools/drive-transcript-watch.py`: downloads transcripts from Drive
- `_Vault/Atlas/agent/tools/meeting-ingest-worker.py`: processes transcripts autonomously (Claude API)
- `_Vault/Atlas/agent/tools/create_issues_from_meeting.py`: creates Linear issues + Backlog-Inbox
- `_Vault/Atlas/agent/tools/google-auth-setup.py`: one-time Google OAuth auth flow
- `Agents/Workflows/WF-meeting-ingest.md`: meeting processing rules
- `_Vault/Atlas/Atlas.md`: VPS and cron configuration

---

*FinCo-OS · Agents/Workflows/WF-google-drive-transcript-watch.md · v2.0 · 2026-06-22*
