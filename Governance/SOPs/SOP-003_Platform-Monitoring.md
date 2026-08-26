---
type: sop
code: SOP-003
title: Platform Monitoring
owner: Chief of Staff
status: active
version: 1.1
reviewed: 2026-07-03
review_cycle: quarterly
supersedes: null
---

# SOP-003: Platform Monitoring

## Purpose

Ensure that FinCo's technical collection operation functions correctly on a continuous basis. Detect anomalies before they impact clients or debtors. Controls are preventive, not reactive.

> "We are no longer a development group; this is now a company that operates." (Elena Torres)

> **Boundary with SOP-008 (2026-07-03, finding A7 from the audit):** this SOP covers **platform technical health**: are the systems working? (active lines, error rates, deliverability). `SOP-008_Operational-Monitoring` covers **campaign performance**: is collection being effective? (funnel, RNP, promises, conversion), translating the principles of `POL-006_Operational-Monitoring`. A channel can be 🟢 in SOP-003 (technically working) and 🔴 in SOP-008 (not converting).

## Scope

All technical collection channels: telephony (**LiveKit**: main engine since DEC-001, and **ElevenLabs**: in production during the transition), WhatsApp (WATI/Nova), SMS, cost per case.

---

## Daily checklist (Monitor-Agent)

The Monitor-Agent runs this checklist every business day. Thresholds are in `Operations/Monitoring/Thresholds.md`.

| Component | What to verify | Alert if |
|---|---|---|
| **Telephony (LiveKit)** | Are lines active? Voicemail detection <6s? Errors in recent calls? | Error rate >5%, inactive lines, or voicemail detection >6s |
| **Telephony (ElevenLabs)** | Are lines active? Errors in recent calls? (in production during transition to LiveKit) | Error rate >5% or inactive lines |
| **WhatsApp / Nova** | Are templates sending? Failed messages? Normal delivery rate? | Delivery rate <90% or rejected templates |
| **SMS** | Volume within agreed limits? Cost per SMS within range? | Volume >110% of weekly average |
| **Cost per case** | Is cost per managed account within range? | Deviation >20% from baseline |
| **Nova, active conversations** | Is the agent responding? Conversations without response >24h? | Stuck conversations >10 |

---

## Response scale

| Level | Condition | Action | Maximum time |
|---|---|---|---|
| 🟢 **Normal** | Everything within thresholds | Log in `Operations/Monitoring/` | — |
| 🟡 **Attention** | One metric out of threshold | Notify in `#finco-ops` + investigate | 2 hours |
| 🔴 **Incident** | Critical system down or multiple metrics out of threshold | Activate incident protocol + escalate to Director of Operations + CEO FinCo | 30 min |

---

## Incident protocol

1. Monitor-Agent detects the anomaly and posts in `#finco-ops` with context.
2. Chief of Staff confirms the incident and activates response.
3. If it impacts production: escalate to Director of Operations + CEO FinCo immediately.
4. Open entry in `Operations/Monitoring/Incidents/YYYY-MM-DD-[description].md`.
5. Resolve the incident.
6. Post-mortem documented in the same entry (root cause, corrective action, prevention).
7. If the root cause reveals a process gap: update the relevant SOP (PDCA Act cycle).

---

## Cost baseline (June 2026 reference)

| Channel | Current monthly cost | Source |
|---|---|---|
| ElevenLabs (voice) | ~$4,151/mo (platform) + ~$2,254/mo (LLM) = **~$6,405/mo** | May 2026 analysis |
| WATI (WhatsApp) | **$45–73/mo** (plan + messages) | Conservative estimate |
| Blended cost per voice minute | **$0.1242/min** | ElevenLabs + LLM combined |

> Verify against actual billing before reporting to management. See `Controlling/Vendor-Costs.md` for full analysis.

---

## Executed by

Monitor-Agent (`Agents/Monitor-Agent.md`). Results logged in `Operations/Monitoring/`.

---

## Changelog

- **v1.1 (2026-07-03):** fixes A7 from the audit, adds LiveKit (blind until now despite being the main engine since DEC-001); explicit boundary with SOP-008.
- **v1.0 (2026-06-08):** initial version.

---

*FinCo-OS · SOP-003 · v1.1 · 2026-07-03*
