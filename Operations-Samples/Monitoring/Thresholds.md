---
type: operations
updated: 2026-07-03
owner: Chief of Staff
status: draft
---

# Alert Thresholds: FinCo Monitoring

> ⚠️ **Pending validation.** These thresholds are initial estimates. [[Carlos-Ruiz]] must validate them against real operation before Monitor-Agent uses them autonomously.

---

## Telephony / LiveKit (main engine since DEC-001)

| Metric | 🟢 Normal | 🟡 Attention | 🔴 Incident |
|---|---|---|---|
| Call error rate | <5% | 5–10% | >10% |
| Active lines | 100% | — | Any inactive line |
| Voicemail detection | <6s | 6–10s | >10s |

> Thresholds inherited from ElevenLabs for continuity, pending specific validation for LiveKit with [[Carlos-Ruiz]] or [[Isabella Torres]].

## Telephony / ElevenLabs (in production during transition)

| Metric | 🟢 Normal | 🟡 Attention | 🔴 Incident |
|---|---|---|---|
| Call error rate | <5% | 5–10% | >10% |
| Active lines | 100% | — | Any inactive line |
| Average response latency | <2s | 2–4s | >4s |

---

## WhatsApp / Nova / WATI

| Metric | 🟢 Normal | 🟡 Attention | 🔴 Incident |
|---|---|---|---|
| Delivery rate | >95% | 90–95% | <90% |
| Templates rejected by Meta | 0 | 1–2 in 24h | >2 in 24h |
| Failed messages | <2% of volume | 2–5% | >5% |

---

## Nova conversations

| Metric | 🟢 Normal | 🟡 Attention | 🔴 Incident |
|---|---|---|---|
| Conversations unanswered >24h | <5 | 5–10 | >10 |
| Escalations to human unattended | 0 | 1–2 | >2 |

---

## SMS

| Metric | 🟢 Normal | 🟡 Attention | 🔴 Incident |
|---|---|---|---|
| Daily volume vs. weekly average | ±10% | 10–20% higher | >20% higher |
| Cost per SMS vs. baseline | ±5% | 5–10% higher | >10% higher |

---

## Channel costs

| Metric | 🟢 Normal | 🟡 Attention | 🔴 Incident |
|---|---|---|---|
| ElevenLabs monthly total | <$4,000 | $4,000–$6,000 | >$6,000 |
| WATI monthly total | <$500 | $500–$800 | >$800 |

*Reference baseline: `Controlling/Vendor-Costs.md`*

---

## Pending definition with Carlos Ruiz

- [ ] Active simultaneous conversations threshold (Nova capacity)
- [ ] Daily SMS volume baseline
- [ ] Average call duration threshold (to detect abnormally long conversations)
- [ ] Monitoring hours (is it checked outside collections hours?)

---

*FinCo-OS · Operations/Monitoring/Thresholds.md · 2026-07-03*
