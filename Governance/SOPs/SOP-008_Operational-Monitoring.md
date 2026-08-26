---
type: sop
code: SOP-008
title: Operational Monitoring
owner: Operations Manager
status: draft
version: 0.3
reviewed: 2026-07-03
review_cycle: quarterly
supersedes: null
---

# SOP-008: Operational Monitoring

> **Interim owner:** [[Carlos-Ruiz]] (Director of Operations) while JD-008 (Operations Manager) is vacant, decision by Rodney, 2026-07-03 (finding A7/M8 from the audit). He is also the primary source of this SOP's content (knowledge extraction session 2026-07-01). The formal document owner remains the Operations Manager role; Carlos acts by direct hierarchy while the role is filled.

## Purpose

Define the four tiers of operational review for [[FinCo]]: what is reviewed, when, by whom, with what thresholds, and what action is taken when an indicator crosses a threshold. This SOP is the operational translation of [[POL-006_Operational-Monitoring]].

*Primary source: knowledge extraction session with [[Carlos-Ruiz]], Director of Operations, 2026-07-01.*

> **Boundary with SOP-003 (2026-07-03, finding A7 from the audit):** this SOP translates `POL-006_Operational-Monitoring` and covers **campaign performance**: is collection being effective? (funnel, RNP, promises, conversion rate). `SOP-003_Platform-Monitoring` covers **platform technical health**: are the systems working? A channel can be 🟢 in SOP-003 and 🔴 here if it works technically but does not convert.

## Scope

Applies to every active collection campaign managed by [[FinCo]], regardless of creditor. Executed by the [[Operations-Manager]] with support from [[Ops-Agent]] (Atlas) for report automation.

## Fundamental rule

> Active campaign dashboards are updated at frequency ≤2 hours during operational days. Seeing results only at end of day eliminates real-time correction capability.

---

## KPI Taxonomy: The Collections Funnel

All indicators in this SOP derive from the funnel. KPIs are organized by stage:

| Stage | KPI | Review tier |
|---|---|---|
| Managements | Total managements | T1, T2 |
| Managements | Effective managements | T1, T2 |
| Managements | Non-effective managements | T1, T2 |
| Managements | Effectiveness rate (effective ÷ total) | T1, T2, T3 |
| Direct contact | RNP captured (reason for non-payment) | T2, T3 |
| Promises | Valid payment promises | T1, T2 |
| Promises | Conversion rate (promises ÷ direct contacts) | T2, T3 |
| Payments | Payments made (quantity and amount) | T2 |
| Payments | Promise fulfillment rate (individual case) | T2, T3 |
| Cost | Cost per management per channel | T3, T4 |
| Cost | Cost per peso recovered *(aspirational, requires payment confirmation loop with creditor)* | T3, T4 |
| Data | Contact base quality (% valid) | T3 |

**Canonical definitions:**
- **Effective management:** any contact that has an impact on the debtor, including delivered SMS, opened email, message left with a third party, or call with direct contact.
- **Non-effective management:** management where resources were invested but produced no possible result, out-of-service number, wrong number, undelivered SMS, exchange error.
- **Valid promise:** three required fields, specific amount + specific date + payment method. Without one of the three: "attempt."
- **RNP:** documented reason why the debtor did not pay in that instance (unemployment, illness, refusal, not located, etc.).

---

## TIER 1: Real-Time Pulse (every 1–2 hours)

**Cadence:** Every hour during active campaign. Minimum acceptable: every 2 hours.
**Responsible:** [[Operations-Manager]] + [[Ops-Agent]] (Atlas, automatic alert if threshold crossed).
**Review channel:** Real-time operational dashboard ([[Product-Owner]], pending build).

### Indicators to review

| Indicator | Normal threshold | Alert threshold | Action if crossed |
|---|---|---|---|
| Effective management rate | ≥35% | <20% | Review database quality; change channel mix |
| Non-effective management rate | ≤65% | >80% | Immediate technical alert, diagnose channel failure |
| Accumulated payment promises | Proportional to time of day | 0 promises with >200 managements | Diagnose technical or strategic failure (see runbook below) |
| Delivery failures per channel | — | Any channel at 0% delivery | Technical escalation to [[Lead-Engineering]] ≤30 min |

### Diagnostic runbook: zero promises with high volume

```
Many managements + 0 payment promises
          ↓
Are call audios/transcripts arriving complete?
    No → Technical channel failure → escalate to the on-call lead
    Yes ↓
Do SMS have positive delivery reports?
    No → Provider failure → change provider or channel
    Yes ↓
Is the database recent (<48h delinquency age)?
    No → Outdated data → request update from creditor
    Yes ↓
Review AI prompts, possible strategy failure
→ Activate self-correction: analyze interactions with Claude, apply improvement
```

---

## TIER 2: End of Day

**Cadence:** End of each campaign operational day.
**Responsible:** [[Operations-Manager]].
**Deliverable:** Daily report, available on dashboard + downloadable.

### Daily report content

The daily report covers the complete funnel cycle for the day:

1. **Managed accounts**: total debtors contacted in the day
2. **Managements by channel**: calls, SMS, email, WhatsApp, turbocollection (totals and effective/non-effective per channel)
3. **Effectiveness rate by channel**: to compare performance across channels
4. **Payment promises**: quantity, total committed amount, individual detail (debtor, amount, date, method)
5. **Payments made**: quantity and total amount; representation in money recovered for the day
6. **Reasons for Non-Payment (RNP)**: distribution of the day's reasons
7. **Delivery report**: delivery confirmation by channel (SMS: delivered/failed; email: delivered/opened/clicks)
8. **Detailed downloadable report**: export of all day's managements, for audit and for transfer to the creditor's CRM

### Delivery report

The delivery report by channel (SMS delivered/failed, email opened/clicks) is included in the daily report when the provider supplies it. Actively pursue that all providers supply it and that payment is based on effectively delivered messages. Where the market does not offer this, document the gap and manage provider replacement as a priority.

### Active promise tracking

At end of day, the engine checks active promises and triggers:
- Reminder T-2 days before the promised date
- Reminder T-1 day before
- Reminder on the promise day itself

If a call is not possible, use the available alternative channel (SMS, email). The debtor who promises has a 24-hour compliance window (2 days before → 3 days after the promised date) before the promise is marked as broken.

---

## TIER 3: Weekly Review *(draft, pending validation with Carlos Ruiz, strategy engine session)*

**Cadence:** Friday afternoon (integrate with Check/Act/Plan of [[SOP-001_Weekly-Cycle]] or separate meeting if operations warrant it).
**Responsible:** [[Chief-of-Staff]] + [[Operations-Manager]].
**Duration:** 30 minutes.

### Proposed agenda

**0:00–0:10, WEEK CHECK**
- Accumulated weekly funnel: total/effective managements, promises, payments
- Rolling 4 weeks: is the effectiveness rate improving, stable, or deteriorating?
- Promises due this week: how many were fulfilled? (weekly kept promise rate)

**0:10–0:20, DIAGNOSIS**
- Which channel had the best performance this week?
- Any database anomaly? (out-of-service numbers, incomplete demographic data)
- Any pattern in RNPs that suggests a strategy adjustment?

**0:20–0:30, DECISIONS**
- Change channel mix for the next week?
- Adjust AI prompts? (activate self-correction cycle)
- Request data update from the creditor?

### Key weekly KPIs *(draft)*

| KPI | Reference benchmark |
|---|---|
| Accumulated weekly effectiveness rate | ≥35% |
| Weekly kept promise rate | *TBD, validate with Carlos* |
| Weekly payments (amount) | *TBD per portfolio* |
| RNP captured / total managements | *TBD, validate with Carlos* |

---

## TIER 4: Monthly Review *(draft, pending validation with Carlos Ruiz and Alex Carver)*

**Cadence:** First business Monday of the following month.
**Responsible:** [[Chief-of-Staff]] + [[Operations-Manager]] + [[CEO-FinCo]].
**Duration:** 60 minutes.
**Secondary audience:** Creditors (executive report).

### Proposed content *(draft)*

**Monthly scorecard:**

| KPI | Target | Actual | Status |
|---|---|---|---|
| Average monthly effectiveness rate | ≥35% | — | — |
| Monthly kept promise rate | *TBD* | — | — |
| % of managements with RNP captured | *TBD* | — | — |
| Cost per peso recovered *(aspirational)* | *TBD* | — | — |
| Monthly amount recovered | *TBD per portfolio* | — | — |
| Promises fulfilled vs. promised | *TBD* | — | — |
| Interaction report delivered to creditor | 100% | — | — |

**Creditor report:**
- Successfully contacted accounts vs. not contacted
- Out-of-service numbers identified (for creditor CRM update)
- Monthly RNP distribution
- Payments made in the period
- Interaction transcripts and logs (downloadable)

---

## SOP quality metrics

The SOP was executed correctly when:
- The Tier 1 dashboard was updated at frequency ≤2h without interruptions on all active campaign days
- The Tier 2 daily report is available before 8pm on the operational day
- No Tier 1 alert threshold was crossed without corrective action being recorded on the same day
- Available delivery reports by channel are included in the daily report

---

## Executed by

[[Operations-Manager]] with support from [[Ops-Agent]] for automated alerts. See `Agents/Ops-Agent.md` for the agent logic. Detailed dashboard automation workflow pending definition after Daniela's build.

---

*FinCo-OS · SOP-008 · v0.3 · 2026-07-03*
