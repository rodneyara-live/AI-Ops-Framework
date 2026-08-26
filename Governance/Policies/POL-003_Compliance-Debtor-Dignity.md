---
type: policy
code: POL-003
title: Compliance & Debtor Dignity Policy
owner: Compliance Manager
status: active
version: 1.2
reviewed: 2026-07-02
review_cycle: biannual
supersedes: null
---

# POL-003: Compliance & Debtor Dignity Policy

## Purpose

Ensure that all FinCo collection activities respect debtor dignity, comply with the Dominican legal framework, and protect clients (creditors) from legal and reputational risk.

---

## Legal framework

- **applicable local data-protection regulations**: Personal Data Protection of the Dominican Republic
- **Law 310-14**: Regulation of unsolicited commercial email
- **applicable local debt-collection regulations**: Regulation of Debt Collection Activity ⚠️ **Effective: August 3, 2026**
- **Meta / WhatsApp Business Regulations**: acceptable use of messaging templates
- Code of Collection Ethics (adapted from FDCPA for the Dominican context)

---

## Dignity principles

**1. Contact hours.** Debtors are contacted only during acceptable hours: Monday–Friday 8:00am–8:00pm, Saturdays 9:00am–1:00pm. No contact on Sundays or public holidays.

**2. Contact frequency.** Maximum 3 contact attempts per day per debtor, regardless of channel. The system must enforce this limit; it is not optional.

**3. Language.** All communication is professional, clear, and free of threatening, humiliating, or degrading language. Nova and voice templates are approved by Compliance before activation.

**4. Identification.** FinCo always identifies itself when contacting a debtor. Impersonation or creating artificial urgency is not permitted.

**5. Right to dispute.** If a debtor disputes a debt, the process pauses on that account and escalates to the client (creditor) for verification. Nova does not escalate pressure on an account under active dispute.

**6. Third-party data.** Family members, employers, or references are not contacted without prior documented authorization from the debtor.

---

## Template approval

Every new Nova (WhatsApp), voice (ElevenLabs), or SMS template must go through:

1. Draft by the technical team.
2. Compliance Manager review, compliance with this policy.
3. CEO FinCo approval, client dispatch gate.
4. Staging test before production.
5. Registration in `Knowledge-Base/Compliance/Plantillas-Aprobadas.md`.

---

## Sensitive case escalation

Agents automatically escalate to a human when:

- The debtor mentions a vulnerable situation (serious illness, recent unemployment, family bereavement).
- The debtor explicitly asks to speak with a human.
- The debtor disputes the debt or requests documentation.
- The system detects a possible error in the amount or account.
- The debtor expresses significant emotional distress.

Escalation goes to the Operations Manager or the available operations DRI.

---

## Required technical controls: applicable local debt-collection regulations

> Effective: **August 3, 2026.** These controls must be implemented and verified before that date. DRI: Compliance Manager + Lead Engineering.

| Control | Requirement | Implementation |
|---|---|---|
| **Maximum frequency** | Max 3 attempts per debtor per day (any combined channel) | Rate limiting in Nova, voice, and SMS with unified counter per number/ID |
| **7-day window** | Max 7 contacts per debtor per week | Weekly counter per debtor, resets every Monday |
| **Permitted hours** | Monday–Saturday 8:00am–8:00pm only | Timestamp validation before each send; reject outside window |
| **Automated identification** | System must identify as a collection agent on first contact | Mandatory identification text in opening of every Nova and voice script |
| **Opt-out within <24h** | Process no-contact requests in less than 24 hours | Flag in Odoo; Nova and voice verify flag before initiating contact |
| **Audit log** | Immutable record of every contact attempt (who, when, channel, result) | Log in Supabase with timestamp, number, channel, debtor response |
| **Voice recording** | All calls recorded and retained for 2 years | ElevenLabs/Twilio → recordings bucket with retention configured |

**Pre-August 3, 2026 verification:** Compliance-Agent runs `WF-compliance-check` with technical audit of all 7 controls. Result goes to `Operations/Cadences/Monthly-Review/` and requires Compliance Manager approval before the effective date.

---

## Audit

The Compliance Manager reviews a monthly sample of Nova conversations and voice calls to verify compliance with this policy (quality at the source). Findings are documented in `Operations/Cadences/Monthly-Review/`.

---

*FinCo-OS · POL-003 · v1.2 · 2026-07-02*
