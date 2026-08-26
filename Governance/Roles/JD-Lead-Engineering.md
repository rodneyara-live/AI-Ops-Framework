---
type: job-description
code: JD-002
title: Lead Engineering
owner: Chief of Staff
status: draft
version: 0.3
reviewed: 2026-06-15
---

# Lead Engineering

## Role purpose

Lead Engineering is the owner of FinCo's technical architecture. They are the definitive technical voice in system design decisions, code quality, and development governance. They operate as a technical peer to the CEO/Chief AI Officer and as a reference of judgment for the engineering team.

This role does not involve people management. Its authority is technical: defines how things are built, approves what enters production, and detects when the system is accumulating debt that compromises operations.

---

## Responsibilities

**Architecture and design**
- Design and document key technical system decisions (ADRs)
- Define data patterns, authentication, integration, and token management for the stack
- Review and approve PRs with architectural impact before merge to `main`
- Be the team's technical consultation point for design questions

**Development governance**
- Maintain and evolve the technical review checklist
- Ensure compliance with the branch flow and CI/CD defined in the governance SOP
- Detect active technical debt and propose a reduction plan within the weekly cycle
- Ensure merges to `main` undergo adequate technical review

**Platform stability**
- Participate in diagnosis and resolution of high-impact production incidents
- Define technical acceptance criteria for new features
- Ensure changes comply with compliance technical requirements (applicable local debt-collection regulations: rate limiting, audit log, opt-out, automated system identification)

---

## Role competencies

**Technical**
- Backend architecture: REST APIs, relational databases, queue systems, workflow automation
- Design and review of production code, not just implementation
- Technical decision-making with incomplete information, documented clearly
- Natural flow with collaborative development tools (GitHub, CI/CD, PR review)

**Operational**
- Judgment to distinguish when a solution is good enough and when it is not
- Ability to communicate technical decisions to non-technical profiles
- Management of technical debt as a business variable, not just an engineering problem

---

## Scope of authority

| Decision | Authority |
|---|---|
| Approval or rejection of PRs with architectural impact | Autonomous |
| Definition of technical review checklist and PR rules | Autonomous |
| Branching standards and CI/CD flow | Autonomous |
| Data, authentication, and integration patterns for the stack | Autonomous |
| Adoption of new tools or technical dependencies | Coordinated with Head of Operations & Product |
| Changes to production data architecture | Coordinated with CEO |
| Technical decisions with compliance impact (applicable local debt-collection regulations) | Gate: CEO |

---

## Stack

Supabase · N8N · GitHub · Linear · Slack · Python / Node.js · Docker · Claude API · ElevenLabs · WATI

---

*FinCo · Governance/Roles/JD-Lead-Engineering · v0.3 · 2026-06-15*
