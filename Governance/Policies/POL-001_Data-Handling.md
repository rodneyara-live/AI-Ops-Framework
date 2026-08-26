---
type: policy
code: POL-001
title: Data Handling Policy
owner: Chief of Staff
status: active
version: 1.0
reviewed: 2026-06-08
review_cycle: annual
supersedes: null
---

# POL-001: Data Handling Policy

## Purpose

Define how FinCo handles debtor, client, and internal operations data to ensure privacy, security, and legal compliance.

## Applicable legal framework

- **applicable local data-protection regulations**: Personal Data Protection of the Dominican Republic
- **Law 310-14**: Regulation of commercial email sending
- WATI and Meta regulations for WhatsApp Business

---

## Principles

**1. Debtor data is confidential by default.** ID numbers, full names, case numbers, amounts, payment history, and debtor contact information are never exposed on unsecured channels (Slack, unencrypted email, general-access documents).

**2. No raw data enters the vault.** FinCo-OS contains processes, decisions, and knowledge, not debtor records or database exports.

**3. Minimum necessary access.** Agents only access the data they need to execute the process. No speculative access.

**4. Traceability.** Every operation involving debtor data leaves an audited record (see `#agent-audit-log` on Slack).

**5. Consent.** Debtors are contacted only through approved channels (WhatsApp, SMS, voice) and with Compliance-reviewed templates.

---

## Data classification

| Type | Examples | Where it may reside |
|---|---|---|
| **Internal operational data** | Aggregate metrics, channel costs, OKRs | FinCo-OS, Linear, Slack |
| **Client (creditor) data** | Company name, business contact, portfolio size | Odoo CRM, restricted access |
| **Debtor data** | ID number, name, amount, history | Source systems only (Ledger-9/Odoo). Never in vault, Slack, or general documents. |
| **Internal financial data** | Burn, runway, pricing model | Confidential C3, leadership only |
| **Credentials and secrets** | API keys, tokens, passwords | Bitwarden only. Never in plain text. |

---

## Operational rules for agents

- Agents may process **aggregate** collection metrics (rates, volumes, costs per channel).
- Agents **may not** read, process, or reproduce individual debtor records.
- Agents **may not** move data between systems without an approved gate (see `Company/Gates.md`).
- Meeting transcripts mentioning specific debtor data are processed into sanitized minutes, individual data is not included.

---

## Non-compliance

Any exposure of sensitive data outside authorized channels is immediately reported to the Operations Manager and Chief of Staff, who escalate to the FinCo CEO and evaluate reporting obligations under applicable local data-protection regulations.

---

*FinCo-OS · POL-001 · 2026-06-08*
