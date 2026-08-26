---
type: sop
code: SOP-006
title: Client Onboarding
owner: Chief of Staff
status: draft
version: 0.1
reviewed: 2026-06-25
review_cycle: on-change
supersedes: null
---

# SOP-006: Client Onboarding

## Purpose

Define the standard process for onboarding a new creditor client onto the [[FinCo]] platform, from commercial proposal approval to the launch of the first collection management campaign. The objective is to ensure every onboarding is consistent, complete, and traceable, regardless of delinquency bracket, portfolio size, or commercial agreement characteristics.

> This SOP is the foundation of [[FinCo]]'s automated strategy engine. In its mature version, most of these steps will be executed by the platform upon portfolio upload. The SOP evolves in parallel with that development.

## Scope

Applies to every onboarding of a new creditor client managing their portfolios through the [[FinCo]] platform. Executed by the [[JD-Chief-of-Staff|Chief of Staff]] in coordination with the [[JD-Product-Backlog-Owner|Product/Backlog Owner · AI Lead]] and the Director of Operations. The [[JD-CEO|CEO FinCo / Chief AI Officer]] approves the strategy before presenting it to the client and authorizes the launch.

## Fundamental rule

No campaign is launched without completing the Decision Checklist (Phase 2) and obtaining explicit Go/No-Go approval (Phase 4). If either is incomplete, the campaign is not activated.

---

## Process flow

```
Commercial approval
        ↓
[P1] Receipt and Qualification
        ↓
[P2] Strategy Definition → Client approval
        ↓
[P3] Technical Configuration
        ↓
[P4] Validation and Go/No-Go → Internal approval
        ↓
[P5] Launch
        ↓
[P6] Initial Monitoring
```

---

## Steps

### P1: Receipt and Qualification

Confirm that the received portfolio is suitable for management and that the minimum required information is available.

1. Receive the portfolio file from the creditor client.
2. Validate that the file contains the required minimum fields. Canonical source: Creditor Portal. For quick implementation, use the list published in the Creditor Portal (Owner: <@U0B5L5Q1VBP>). Record any discrepancies in the onboarding ticket and notify the client for corrections.
3. Confirm the assigned delinquency bracket and the number of cases.
4. Identify and document any data discrepancies (values outside the declared delinquency range, duplicate records, empty fields).
5. Request from the client any additional variables that apply according to the strategy (employment type, public/private sector, age, secondary contact data).

**Deliverable:** written confirmation that the file was reviewed and meets the input quality criteria, or a list of observations for the client to resolve.

---

### P2: Strategy Definition

Internally agree on the management strategy and obtain formal client approval before configuring the platform.

1. Complete the Onboarding Decision Checklist (see Annex A). Each item must be documented with the decision made.
2. Draft the Strategy Document for the client. **[PENDING: define standard document structure, see Pending section]** The document must include at minimum: active channels, campaign cadence, template examples, agent capabilities, and regulatory compliance.
3. The [[JD-CEO|CEO FinCo / Chief AI Officer]] reviews the document internally before sending it to the client.
4. Send the document to the creditor client and receive written approval.

**Deliverable:** Strategy Document approved in writing by the client.

---

### P3: Technical Configuration

Configure all platform components necessary to execute the approved strategy.

1. Configure the active channels (SMS, email, calls; WhatsApp if applicable per agreement).
2. Configure outbound email according to the client's decision:
   - **Client's own SMTP:** coordinate credentials with the client.
   - **[[FinCo]] infrastructure:** create address, request branding assets from the client (logo, sender name, colors).
3. Assign and configure the DID number for callback returns.
4. Configure the AI agents:
   - **Outbound agent:** objection handling, callback registration, and payment promises.
   - **Inbound agent:** handling all callback returns.
   - **Email agent:** responding to incoming compliance emails.
5. Adapt the communication templates per channel (SMS, email, WhatsApp if applicable) according to the scripts approved in P2.
6. Configure the daily delinquency days update mechanism (method and frequency per agreement with the client).
7. Configure automatic payment promise reminders: day before and day of the promise. This behavior is **fixed standard**: always applies, without exception.
8. Configure the client dashboard with the agreed metrics: contacted cases, non-contacted cases, out-of-service numbers, registered payment promises, confirmed payments.

**Deliverables:** agents configured and in test, templates under review, DID assigned, dashboard accessible.

---

### P4: Validation and Go/No-Go

Verify that all configuration is correct before activating the first campaign.

1. Run delivery tests (DLR test) on each active channel to confirm messages are delivered.
2. Validate that agents respond correctly to test scenarios: common objections, callback requests, payment promise registration.
3. Review that templates reflect the branding and message approved by the client.
4. Confirm that the delinquency data update mechanism is operational.
5. The [[JD-CEO|CEO FinCo / Chief AI Officer]] issues the formal Go/No-Go decision.

   Formal approval criteria (proposal based on team input):

   - Plan payment confirmed and client created in Odoo (commercial activation tied to payment).
   - Minimum file fields complete and validated (see P1).
   - Sample ingestion without critical errors (formats, encoding, duplicates).
   - Legal/compliance validation completed (consents and recording policy reviewed).
   - Minimum contactability in the sample (operational threshold to be defined).

   If any condition is not met → No-Go and plan of corrections/documentation of required actions.

**Deliverable:** written Go/No-Go approval from the [[JD-CEO|CEO FinCo / Chief AI Officer]].

---

### P5: Launch

Activate the campaign and notify relevant parties.

1. Upload the validated portfolio to the [[FinCo]] platform.
2. Activate the campaign according to the cadence defined in P2.
3. Notify the creditor client of the exact start date and time.
4. Record in the client file: launch date, active channels, configured cadence, approved Strategy Document version.

---

### P6: Initial Monitoring

Supervise the first campaign runs to detect and correct operational anomalies.

1. Review dashboard metrics during the first 48 hours.
2. Verify that automatic payment promise reminders are executing correctly.
3. Monitor non-contacted cases: distinguish between out-of-service numbers, incorrect data, or channel failure.
4. Apply the bracket escalation protocol for any case exceeding the upper limit of the assigned bracket (see Escalation Protocol section).
5. Document findings and incidents in the client file.

---

## Fixed platform standards

The following parameters apply to all clients without exception and are non-negotiable in the onboarding process:

| Standard | Description |
|---|---|
| Payment promise reminder | Automatically sent the day before and the day of the promise, on the agreed channel. |
| Full callback coverage | All return calls are handled by the inbound agent. No call goes unmanaged. |
| Call recording | All voice interactions are recorded and available for client audit. |
| Regulatory compliance | All operations are executed in conformity with the Criminal Procedure Code (effective August 2026), Indotel regulations, and the alliance with Proconsumidor. |
| Contactability validation | The platform records and reports out-of-service or unreachable numbers so the creditor can update their database. |
| Real-time dashboard | All clients have access to management metrics: contacted, non-contacted, promises, payments. |

---

## Bracket escalation protocol

When a case exceeds the upper limit of the assigned delinquency bracket during active management:

1. The platform automatically detects that the case has exceeded the threshold.
2. Automated management of the case is paused.
3. The creditor client is notified with the case details and its new bracket.
4. The client confirms in writing one of three actions: (a) withdraw the case, (b) reassign it to a higher bracket, or (c) maintain the current strategy.
5. If the case is withdrawn, the platform explicitly records the reason: `paid` or `bracket escalation`. This distinction is critical for reporting integrity.

> **Operational decision (proposal):** Withdrawal defaults to manual per client instruction. FinCo may initiate operational withdrawal if it detects unacceptable data or repeated inability to contact (thresholds to be defined). Withdrawal should not be automated for a simple bracket change without human review.

---

## Annex A: Onboarding Decision Checklist

To be completed in P2 for each client. All checkboxes must be marked before starting P3.

| # | Decision | Options |
|---|---|---|
| 1 | Delinquency bracket(s) to manage | Define exact range. Confirm what happens outside the range. |
| 2 | Active channels | SMS / Email / WhatsApp / Calls, which are enabled. |
| 3 | Outbound email configuration | Client SMTP or [[FinCo]] infrastructure. If [[FinCo]]: request branding assets. |
| 4 | Campaign cadence | Touchpoints per channel per cycle. Cycle duration. |
| 5 | Available data variables | Confirm fields the client provides. Request additional variables if applicable. |
| 6 | Delinquency update mechanism | How and how often delinquency days are updated (API, upload, FTP…). |
| 7 | Bracket escalation protocol | Automatic withdrawal or explicit client instruction? |
| 8 | Data enrichment | Is the portfolio enriched with [[GroupCo]] data? What fields? How is it reported to the client? |
| 9 | Branding assets | If using [[FinCo]] infrastructure for email: logo, sender name, colors. |
| 10 | DID number | Confirm available number and assign it to the client. |
| 11 | Scripts and guides | Approved version of call, SMS and email script, approved by the client. |
| 12 | Dashboard access | Create creditor client access and configure visible metrics. |

---

## Pending items

The following items must be resolved before publishing version 1.0 of this SOP. They are marked as `[PENDING]` in the document body.

|| # | Item | Suggested responsible |
|---|---|---|---|
|| 1 | Minimum required fields to accept a portfolio (P1) | Owner (source of truth): Creditor Portal, operational responsible: <@U0B5L5Q1VBP> and Director of Operations. (URGENT) |
|| 2 | Complete RACI for the process (who approves what in each phase) | Chief of Staff + <@U0B34CNQFUZ> (pending) |
|| 3 | SLAs per phase (maximum times per stage) | <@U0B34CNQFUZ>, Operational target: 1 day end-to-end when the process is fully automated. (URGENT) |
|| 4 | Formal Go/No-Go criteria (P4) | <@U0B3N5QD8HH> + Chief of Staff (define contactability thresholds) |
|| 5 | Standard structure of the Strategy Document for the client (P2) | <@U0B3N5QD8HH> (URGENT) |
|| 6 | Withdrawal automation for bracket escalation | Director of Operations + <@U0B34CNQFUZ> + <@U0B5H67MK2P> (define thresholds and manual/automatic process) |
|| 7 | Call recording retention policy | <@U0B34CNQFUZ> + <@U0B5H67MK2P>, Initial proposal: 90 days (pending final legal review, Criminal Procedure Code August 2026). (URGENT) |

---

## Quality metrics

The process was executed correctly if:

- The Decision Checklist (Annex A) is complete before starting P3.
- Written client approval of the Strategy Document exists.
- Written Go/No-Go approval exists before launch.
- DLR tests passed on all active channels.
- No critical incidents were reported in the first 48 hours of monitoring.

> **[PENDING]** Define quantitative thresholds for each metric (e.g., minimum delivery rate in DLR, maximum incident resolution time).

## Executed by

Manual process coordinated by the [[JD-Chief-of-Staff|Chief of Staff]]. See role table in `Company/Team.md`. Progressive automation planned as part of the [[FinCo]] strategy engine.

---

*FinCo-OS · SOP-006_Client-Onboarding.md · 2026-06-25*
