---
type: job-description
code: JD-008
title: Operations Manager
owner: Director of Operations
status: draft
version: 0.1
reviewed: 2026-07-02
---

# Operations Manager

## Role purpose

The Operations Manager is the executor of [[FinCo]]'s daily operational cycle. While the [[JD-Director-of-Operations|Director of Operations]] designs strategy, defines benchmarks, and programs the engine's intelligence, the Operations Manager ensures that strategy is executed correctly, anomalies are detected in time, and reports arrive when they should.

The role lives at the intersection of execution and first-level diagnosis: able to read the dashboard, know when something is wrong, know whether the first cause is technical or operational, and know whom to escalate to in each case. They do not design the strategy; they execute and defend it.

This position has high potential for agentic automation. Many of its routine functions (threshold alerts, daily report generation, promise follow-up) are direct candidates for execution by the [[Ops-Agent]]. The differential value of the human occupant lies in situational judgment: interpreting what the data says in context, deciding whether a pattern is noise or signal, and coordinating the response when the situation exceeds the agent's logic.

---

## Responsibilities

**Operational monitoring cycle**
- Execute Tier 1 reviews with frequency ≤2 hours during active campaign days: verify funnel indicators, confirm no alert threshold has been crossed, and record the review
- Activate the escalation protocol defined in [[SOP-008_Operational-Monitoring]] within a maximum of 30 minutes when a threshold is crossed, without waiting for confirmation from the [[Director-of-Operations]] for initial activation
- Produce or validate the daily Tier 2 report before 8pm on the operating day: complete funnel, touches by channel, promises, payments, daily RNP, delivery report by channel
- Maintain active follow-up of current payment promises: T-2 days, T-1 day, and the promised day itself

**Campaign execution**
- Coordinate with the [[Director-of-Operations]] the initial configuration of each campaign before activation: enabled channels, loaded database, assigned strategy, defined thresholds
- Monitor active channel execution during the campaign and detect anomalous behavior: channel with 0% delivery, touch volume below expected, absence of promises with high touch volume
- Document any deviation from designed strategy and report it to the [[Director-of-Operations]] with supporting data

**First-level diagnosis and escalation**
- Apply the diagnostic runbook from [[SOP-008_Operational-Monitoring]] as the first step for any anomaly: technical failure or strategy failure? Channel or data? Prompt or database?
- Escalate confirmed technical failures to [[Lead-Engineering]] (lost audio, SMS with 0% delivery, exchange failure) within the timelines defined in the SOP
- Escalate strategy failures or patterns requiring decisions on prompts, segmentation, or channel mix to the [[Director-of-Operations]]
- Document all escalations, corrective actions taken, and results in `#finco-ops`

**Operational reporting**
- Produce periodic campaign reports: daily report (Tier 2), weekly indicator report (Tier 3 in coordination with [[Chief-of-Staff]]), and input for the monthly creditor report (Tier 4)
- Ensure creditor reports include: accounts contacted, RNP distribution, confirmed payments, identified out-of-service numbers, and downloadable transcripts
- Coordinate with the creditor on operational data update requests (outdated databases, invalid phones, delinquencies not updated) based on monitoring findings

**Coordination with the Ops-Agent**
- Verify daily that the [[Ops-Agent]] (Atlas) correctly executed the cycle's automated tasks: alerts, reports, promise follow-ups
- Detect and escalate to [[Chief-of-Staff]] any agent failure or unexpected behavior affecting the operational cycle
- As new functions are automated, assume the role of quality validator of agent output before it reaches the [[Director-of-Operations]] or the creditor

---

## Role competencies

**Technical**
- Reading and interpreting the collection funnel: ability to look at an active campaign dashboard and diagnose whether ratios are normal, which stage is failing, and what the most likely cause hypothesis is
- Applying the diagnostic runbook: distinguishing technical failure from strategy failure without case-by-case instruction, using the protocol defined in [[SOP-008_Operational-Monitoring]]
- Operational handling of automated collection channels: knows what an SMS delivery report means, what an incomplete call audio indicates, what differentiates an effective touch from an ineffective one in each channel

**Operational**
- Cycle discipline: executes monitoring at the established frequency without needing reminders, and documents evidence of each review
- Escalation judgment: knows when to resolve at their level, when to escalate to the Director, when to escalate to Engineering, and does not confuse the three
- Operational communication: writes accurate, actionable reports; the receiver knows exactly what happened, what was done, and what remains pending
- Agentic judgment: understands which part of their work is a candidate for automation and actively works to transfer it to the agent, retaining the situational judgment the agent cannot replicate

---

## Scope of authority

| Decision | Authority |
|---|---|
| Activate escalation protocol on threshold breach | Autonomous |
| Escalate confirmed technical failure to Lead Engineering | Autonomous |
| Tactical channel adjustment within established strategy | Coordinated with [[Director-of-Operations]] |
| Request data update from creditor (operational) | Autonomous |
| Campaign strategy or prompt changes | Gate: [[Director-of-Operations]] |
| Communication to creditor with contractual implications | Gate: [[Director-of-Operations]] |

---

## Work stack

[[Ops-Agent]] (Atlas) · Ledger-9 (CRM GroupCo) · Operational dashboard ([[Product-Owner]], pending build) · [[Nova]] · [[Conductor]] · FinCo-OS (Obsidian) · Slack (`#finco-ops`) · Linear

---

## Note on agentic automation

This role is the closest in the current structure to the work that the [[Ops-Agent]] executes or will execute. The distinction justifying maintaining the human figure is contextual judgment: the agent can alert when a threshold is crossed; it cannot decide whether the anomaly is a transient provider failure or the beginning of a campaign deterioration requiring a strategy change. That interpretation requires collection operational experience that the agent currently does not have and that the Operations Manager must provide.

The natural evolution of this role is for the occupant to progressively spend less time executing routine tasks, which the agent absorbs, and more time validating, interpreting, and coordinating the exceptions the agent cannot resolve alone.

---

## Version notes

| Version | Date | Change |
|---|---|---|
| 0.1 | 2026-07-02 | Initial version. Vacant position. Based on operational scope defined in [[SOP-008_Operational-Monitoring]] and [[POL-006_Operational-Monitoring]] |

---

*FinCo · Governance/Roles/JD-Operations-Manager · v0.1 · 2026-07-02*
