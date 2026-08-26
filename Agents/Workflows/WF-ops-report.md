---
type: workflow
code: WF-ops-report
owner: Chief of Staff
agent: Atlas
status: active
version: 1.2
reviewed: 2026-07-03
---

# WF-ops-report: Operational report and on-demand analytics

**Executed by:** Atlas (VPS crons + conversational Slack response)
**Scripts:** `ops-report-daily.py`, `ops-analytics.py`

---

## Summary

Two complementary flows provide full analytical coverage of the FinCo operation:

1. **Automatic daily report**: one cron captures a local backup in the afternoon (without publishing) and another publishes the mini-report of the closed day in `#finco-ops` each morning, archiving the payload for historical analysis.
2. **On-demand analytics**: when someone asks about metrics for a client or trend, Atlas invokes `ops-analytics.py` and responds with real data.

---

## Architecture

```
FLOW 1: Daily report
═══════════════════════════════════════════════════════════

Cron 1: 22:30 UTC (6:30 PM RD), ops-report-daily.py --capture
  → Calls /raw?preset=today (live data at day close)
  → Saves state locally (save_state), does NOT publish to Slack
  → Backup only: if the next morning's --send cron
    cannot get fresh data from the API, this backup does not directly
    replace it (--send uses its own fallback to local snapshot_slack_ready
    data), its real function is to leave early evidence of the close
    in case diagnostics are needed

Cron 2: 12:15 UTC (8:15 AM AST), ops-report-daily.py --send  [v3.0, 2026-07-02]
  → Calls /raw?preset=yesterday → reads ONLY data.snapshot_slack_ready
    (pre-calculated block by Andres Vega, reconciles 1:1 with the daily email
    from pipeline@groupco.io, sent at 08:00 AST)
  → Archives the full payload in /srv/finco/state/snapshot-YYYY-MM-DD.json
  → Publishes global report + Fasttrack Lending block in #finco-ops
  → No longer uses /snapshot?fecha=, nova_voice, or traffic light, see
    _Vault/Integrations/API-Report-FinCo.md § Option B (deprecated fields)

State: /srv/finco/state/snapshot-YYYY-MM-DD.json  ←─ historical analytics base


FLOW 2: On-demand analytics
═══════════════════════════════════════════════════════════

Question in Slack (e.g. "how are we doing with Fasttrack Lending?")
  → Atlas detects operational metrics question
  → Atlas runs ops-analytics.py from its terminal
      → Reads local snapshots: /srv/finco/state/snapshot-YYYY-MM-DD.json
      → Fallback: calls /snapshot via API if the local file does not exist
      → Adds: totals, averages, traffic lights per day
  → Atlas publishes Slack-formatted output in the thread
```

---

## Operational API endpoints

See full documentation in `_Vault/Integrations/API-Report-FinCo.md`.

| Endpoint | Usage | Cron |
|---|---|---|
| `GET /raw?preset=today` | Live data (any time) | `--capture` 22:30 UTC |
| `GET /raw?preset=yesterday` → `snapshot_slack_ready` | Closed day, single source for the mini-report (v3.0) | `--send` 12:15 UTC |
| `GET /snapshot?fecha=YYYY-MM-DD` | Deprecated for the mini-report — see `API-Report-FinCo.md` | — |

Andres Vega's daily email (`pipeline@groupco.io`) is sent at 08:00 AM AST, the `--send` cron runs 15 min later (12:15 UTC / 08:15 AST) to ensure the pipeline has already closed.

---

## Operational traffic lights

**The daily mini-report (Cron 2 / `--send`) no longer has a traffic light**: removed along with the deprecated fields (v3.0, 2026-07-02). The table below applies only to ad-hoc/hourly alerts on raw data (not to the daily mini-report):

| Metric | Green | Yellow | Red |
|---|---|---|---|
| Delivery rate (call) | ≥ 70% | 50–70% | < 50% |
| Failed Eleven / dispatched | — | — | ≥ 40% → alert |
| Dispatcher error | — | — | ≥ 25% → alert |
| Failure rate by client | — | — | > 30% → escalate |

---

## Invoking ops-analytics.py

```bash
# Last week, global
python3 /srv/finco/repos/FinCo-OS/_Vault/Atlas/agent/tools/ops-analytics.py \
  --period=7d --format=slack

# Specific client
python3 /srv/finco/repos/FinCo-OS/_Vault/Atlas/agent/tools/ops-analytics.py \
  --cliente=789 --period=7d --format=slack

# Custom range
python3 /srv/finco/repos/FinCo-OS/_Vault/Atlas/agent/tools/ops-analytics.py \
  --from=2026-06-01 --to=2026-06-25 --format=slack
```

Frequent IDs: Fasttrack Lending = 789, BHD-BPO = 273.

---

## Fallback chain: historical data

```
1. Read local snapshot: /srv/finco/state/snapshot-{date}.json
2. If it doesn't exist → GET /snapshot?fecha={date} via API
3. If the API fails → GET /raw with current day's date
4. If everything fails → notify DRI (#finco-ops) with date without data
```

Historical coverage starts 2026-06-26. First full week available ~2026-07-03.

---

## Actors and channels

| Actor | Role |
|---|---|
| `ops-report-daily.py` | Publishes automatic daily reports |
| `ops-analytics.py` | Answers on-demand questions with aggregated data |
| Atlas | Detects metrics questions in Slack, invokes the script, publishes output |
| `#finco-ops` | Main channel for publishing reports and responses |
| Rodney / Alex Carver (DRI) | Manual escalation if there are red alerts or missing data |

---

## Triggers

| Trigger | Flow activated |
|---|---|
| Cron 22:30 UTC | `--capture` → local backup capture (no publish) |
| Cron 12:15 UTC | `--send` → daily mini-report (global + Fasttrack Lending) + local archive |
| Metrics question in Slack | Atlas → `ops-analytics.py` → response in thread |

---

## Outputs

| Output | Destination | Frequency |
|---|---|---|
| Local backup capture | `/srv/finco/state/ops-report-YYYY-MM-DD.json` | Daily 6:30 PM AST |
| Daily mini-report (global + Fasttrack Lending) | `#finco-ops` | Daily 8:15 AM AST |
| Snapshot archive | `/srv/finco/state/snapshot-YYYY-MM-DD.json` | Daily |
| Analytics response | Slack thread (requesting channel) | On-demand |

---

## Restrictions

- Archived snapshots contain closed operational data, do not mutate them.
- Do not expose `FinCo_OPS_API_TOKEN` in logs or Slack.
- Do not expose individual debtor data (names, ID numbers, balances), only aggregates.
- The script does not have write access to the platform: it is read-only.

---

## References

- `_Vault/Integrations/API-Report-FinCo.md`: Full endpoint documentation
- `_Vault/Atlas/Atlas.md`: VPS cron registry
- `_Vault/Atlas/SOUL.md` § Operational analytical capability
- `_Vault/Atlas/agent/tools/ops-report-daily.py`
- `_Vault/Atlas/agent/tools/ops-analytics.py`

---

## Changelog

- **v1.2 (2026-07-03):** corrects A8 from the audit, the §Architecture diagram said Cron 1 (`--capture`) "builds and publishes" the summary; verified against `ops-report-daily.py` (lines 366-378): `--capture` only saves local state, never calls `post_to_slack`. The §Summary already stated it correctly, the diagram was the misaligned part.
- **v1.1 (2026-07-02):** previous version (v3.0 of the script, snapshot_slack_ready).

---

*FinCo-OS · Agents/Workflows/WF-ops-report.md · v1.2 · 2026-07-03*
