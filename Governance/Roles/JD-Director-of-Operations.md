---
type: job-description
code: JD-007
title: Director of Operations
owner: CEO FinCo
status: draft
version: 0.1
reviewed: 2026-07-02
---

# Director of Operations

## Role purpose

The Director of Operations is the collections intelligence of [[FinCo]]. They do not manage a floor of human agents; they program the logic that governs the AI agents operating at scale. Their function is to translate decades of institutional knowledge about debtor behavior, channel effectiveness, and recovery strategy into the rules, prompts, and decisions that [[Nova]], [[Conductor]], and the strategy engine execute on every account.

In an AI-first company, this role is responsible for the engine knowing what to do. Lead Engineering builds the engine; the Director of Operations programs it. The role's effectiveness is measured not by the number of people coordinated, but by the quality of decisions embedded in the system and the ability to diagnose, and correct, when the engine produces unexpected results.

The Operations Manager reports to this role and is responsible for daily execution. The Director of Operations is responsible for the strategy, knowledge, and standards upon which that execution operates.

---

## Responsibilities

**Collection engine**
- Design and maintain the collection funnel model as the primary analytical lens of the operation: touches → effectiveness → direct contact → RNP/promise → payment
- Establish and calibrate funnel performance benchmarks (effectiveness rate, conversion rate, kept promise rate) based on historical experience and adjusted to [[FinCo]]'s portfolios
- Define operational alert thresholds and ensure the [[JD-Operations-Manager|Operations Manager]] and [[Ops-Agent]] apply them consistently
- Validate that the funnel interpretation as aggregate ratios, not individual chains, remains the analytical principle in all reports and dashboards

**Campaign strategy**
- Design management strategy by delinquency tranche: channels, frequency, tone, contact sequences, and escalation criteria for each window (pre-delinquency, 1–30, 31–60, 61–90 days)
- Determine the optimal channel mix per campaign and portfolio: call, SMS, email, WhatsApp, turbocobro (mute, generic, personalized text-to-speech)
- Design portfolio demographic segmentation when creditor data allows (gender, age, residence, amount), and incorporate it into strategy and manager assignment
- Define valid promise criteria (amount + date + method), maximum acceptable horizons, and post-promise follow-up protocol
- Establish available agreement types by debtor profile: single payment, payment plan, immediate payment discount, moratorium

**Operational monitoring**
- Own the monitoring intelligence: what is measured, at what frequency, with what thresholds, and what action is taken when an indicator crosses a threshold (see [[POL-006_Operational-Monitoring]] and [[SOP-008_Operational-Monitoring]])
- Validate that Tier 1 (≤2h) and Tier 2 (daily close) dashboards faithfully reflect funnel status and that per-channel delivery reports are included when the provider supplies them
- Conduct Tier 3 (weekly) and Tier 4 (monthly) reviews with the [[Chief-of-Staff]] and [[CEO-FinCo]], respectively
- Diagnose root cause when the engine produces anomalous results: distinguish technical failure (lost audio, undelivered SMS, exchange failure) from strategy failure (outdated database, inadequate prompts, incorrect segmentation), and prescribe the correct remedy in each case

**Collections intelligence and AI**
- Be the primary source of knowledge feeding [[Nova]] and the [[Strategy-Engine]]: what an AI says on the first call, how it responds to an unemployment RNP, when it escalates to a payment plan offer
- Conduct self-correction cycles: review interactions, identify failure patterns in debtor conversations, propose prompt improvements, and validate their effectiveness in the next cycle
- Monitor coherence between designed strategy and what the AI agent executes; detect deviations before they accumulate in the funnel
- Keep the vault's collections knowledge base ([[Knowledge-Base/Collections]]) updated with emerging patterns, proven strategies, and learnings by tranche and debtor profile

**Operational relationship with creditors**
- Lead data quality diagnosis during new creditor onboarding: detect out-of-service phones, outdated delinquency data, absence of demographic information, and set performance expectations adjusted to input quality
- Design the initial campaign strategy for each creditor based on portfolio characteristics (predominant delinquency tranche, demographic profile, contactability history)
- Produce or validate periodic reports to the creditor: accounts managed, RNP distribution, confirmed payments, identified out-of-service numbers, and downloadable interaction transcripts

**Operational governance**
- Own documented operational knowledge: keep [[POL-006_Operational-Monitoring]] and [[SOP-008_Operational-Monitoring]] updated and aligned with operational reality
- Ensure [[FinCo]]'s collection operation complies at all times with applicable local debt-collection regulations, the Collection Ethics Code, and INDOTEL and Proconsumidor regulations
- Supervise that the [[JD-Operations-Manager|Operations Manager]] executes SOP procedures without deviation, and that any threshold breach is documented with corrective action

---

## Role competencies

**Technical**
- Mastery of the collection funnel model as an analytical system: ability to read a dashboard, diagnose where performance is lost at which stage, and formulate cause hypotheses with sufficient precision to prescribe the correct remedy
- Collection strategy design by segment: delinquency tranche, demographic profile, debt type, contactability history, and translation of that strategy into operational rules an AI agent can execute
- Campaign data analysis: ratios, trends, benchmarks, variance interpretation between periods, with ability to distinguish statistical noise from real signal
- Knowledge of automated collection channels (call, SMS, email, WhatsApp, turbocobro) and their operational, regulatory, and relative effectiveness constraints

**Operational**
- Judgment to separate technical diagnosis from strategic diagnosis without relying on third-party interpretation: when the engine fails, knows whether the problem is in the channel, the data, the prompt, or the strategy
- Ability to work with AI agents as a primary work tool: not as an occasional user, but as an operator who designs, tests, corrects, and improves agent behavior in short cycles
- Operational communication with creditors: ability to translate technical funnel language into a report the business client can read, interpret, and use to make decisions about their portfolio
- Document management of operational knowledge: what they know does not live only in their head; it lives in the vault, in SOPs, in the knowledge base, so that other roles and agents can operate with it

---

## Scope of authority

| Decision | Authority |
|---|---|
| Campaign strategy per creditor (channels, sequences, tranches) | Autonomous |
| Prompt and conversation logic adjustments for AI agents | Autonomous (coordinated with [[Lead-Engineering]] for integration changes) |
| Alert thresholds and operational escalation protocols | Autonomous |
| Changes to SOP benchmarks and KPIs | Coordinated with [[Chief-of-Staff]] |
| Provider selection and replacement (SMS, voice, TTS) | Coordinated with [[CEO-FinCo]] |
| Operational onboarding of new creditor | Autonomous within [[SOP-006_Client-Onboarding]] |
| Modifications to governance policies (POL-006) | Gate: [[Chief-of-Staff]] |
| Agreements with creditors modifying price or contractual scope | Gate: [[CEO-FinCo]] |

---

## Work stack

Ledger-9 (CRM GroupCo) · [[Nova]] · [[Conductor]] · [[Strategy-Engine]] · Creditor Portal · Power BI · FinCo-OS (Obsidian) · Claude/Cowork · Slack · ElevenLabs · Turbocobro

---

## Version notes

| Version | Date | Change |
|---|---|---|
| 0.1 | 2026-07-02 | Initial version. Based on knowledge extraction session with [[Carlos-Ruiz]], 2026-07-01, and context from [[POL-006_Operational-Monitoring]], [[SOP-008_Operational-Monitoring]] and [[Knowledge-Base/Collections/Collections-Strategies]] |

---

*FinCo · Governance/Roles/JD-Director-of-Operations · v0.1 · 2026-07-02*
