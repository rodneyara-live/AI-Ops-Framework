---
type: operations
updated: 2026-07-03
owner: Chief of Staff
---

# Daily Health Checklist: FinCo

Executed by Monitor-Agent every business day. Alert thresholds are in `Thresholds.md`.

> Current status: **assisted manual**. Thresholds pending validation with [[Carlos-Ruiz]].

---

## Components to verify

### 1. Telephony / LiveKit (main engine since DEC-001)
- [ ] Are the lines active and responding?
- [ ] Call error rate in the last 24h within threshold?
- [ ] Voicemail detection <6s?
- [ ] Any unusual errors in logs?

**Source:** LiveKit Dashboard / logs

---

### 1b. Telephony / ElevenLabs (in production during transition)
- [ ] Are the lines active and responding?
- [ ] Call error rate in the last 24h within threshold?
- [ ] Any unusual errors in logs?

**Source:** ElevenLabs Dashboard / N8N logs

---

### 2. WhatsApp / Nova / WATI
- [ ] Are templates sending correctly?
- [ ] Delivery rate within threshold?
- [ ] Any template rejected by Meta in the last 24h?
- [ ] Unusual failed messages?

**Source:** WATI Dashboard

---

### 2b. Active Nova conversations
- [ ] How many conversations have gone more than 24h without agent response?
- [ ] Are there conversations that escalated to human and were not attended?

**Source:** Supabase / N8N

---

### 3. SMS
- [ ] Daily SMS volume within normal range?
- [ ] Cost per SMS within baseline?

**Source:** SMS Provider

---

### 4. Cost per case (weekly)
*Verify on Mondays.*
- [ ] Is the estimated cost per managed account this week within baseline?
- [ ] ElevenLabs + LLM within range (~$5,200/month)?

**Source:** Provider invoices / dashboards

---

## Result log

Upon completion, record in `Operations/Monitoring/Log-YYYY-MM.md`:

```
## YYYY-MM-DD

| Component | Status | Notes |
|---|---|---|
| Telephony | 🟢/🟡/🔴 | — |
| WhatsApp/Nova | 🟢/🟡/🔴 | — |
| Active conversations | 🟢/🟡/🔴 | — |
| SMS | 🟢/🟡/🔴 | — |
| Cost/case | 🟢/🟡/🔴 | — |

Active incidents: none / [description]
```

---

*FinCo-OS · Operations/Monitoring/Daily-Checklist.md · 2026-07-03*
