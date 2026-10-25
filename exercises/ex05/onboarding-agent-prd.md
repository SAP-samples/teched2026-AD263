# PRD: Employee Onboarding Automation Agent

## Overview

Automate the end-to-end IT and finance onboarding process for new hires by connecting SAP SuccessFactors, SAP S/4HANA, and SAP Ariba into a single agent-driven workflow — eliminating manual handoffs between HR, Finance, and IT teams.

---

## Problem Statement

Today, onboarding a new employee requires coordinated action across three systems and two to three teams:

1. HR creates the employee record in SuccessFactors
2. Finance manually provisions a cost center assignment in S/4HANA
3. IT raises an equipment purchase requisition in Ariba — often days later

Each handoff introduces delay (average 3–5 business days end-to-end) and error risk. New hires frequently start without system access or equipment on Day 1.

---

## Goal

Trigger a fully automated onboarding sequence the moment a new hire record is activated in SuccessFactors. The agent completes all downstream provisioning steps and pauses exactly once — for manager approval of the equipment purchase requisition — before submitting any purchase order.

---

## Personas

| Persona | Role | Interaction with agent |
|---------|------|------------------------|
| **New Hire** | Incoming employee | Passive — benefits from the outcome |
| **Hiring Manager** | Approves equipment requisition | Receives approval request; types CONFIRM or REJECT |
| **HR Administrator** | Manages SuccessFactors records | Triggers the flow by activating the hire record |
| **Finance Controller** | Owns cost center assignments | Reviews agent output; no manual action required |

---

## Business Outcomes

| Metric | Current (manual) | Target (agent) |
|--------|-----------------|----------------|
| Time to full system provisioning | 3–5 business days | < 2 hours |
| Manual steps per onboarding | 12+ | 1 (manager approval) |
| Equipment ordered by Day 1 | ~40% of hires | 100% |
| Audit trail completeness | Partial | 100% labeled output |

---

## Functional Requirements

### FR-1 — Trigger on New Hire Activation
- Monitor SuccessFactors for new employee records with status `Active`
- Extract: employee ID, name, department, job role, manager ID, start date, work location

### FR-2 — Cost Center Provisioning in S/4HANA
- Look up the correct cost center for the employee's department and work location
- Assign the employee to the cost center in S/4HANA
- Label the assignment confirmation as `[Confirmed Fact]`

### FR-3 — Employee Profile Enrichment
- Write job role, cost center, and manager back to the SuccessFactors employee profile
- Label all written values as `[Confirmed Fact]`

### FR-4 — Equipment Requisition in SAP Ariba
- Based on job role, determine the standard equipment bundle (laptop spec, peripherals, access card)
- Create a draft purchase requisition in SAP Ariba with line items per equipment category
- Label equipment selection as `[Recommendation]` (role-based standard, not individually verified)

### FR-5 — Human Approval Gate (MANDATORY)
- Present the full provisioning summary and equipment requisition to the hiring manager
- Show exact prompt:

  ```
  Please review the onboarding provisioning below.
  Type CONFIRM to submit the equipment purchase requisition in SAP Ariba.
  Type REJECT (or any other response) to cancel — no purchase order will be raised.
  ```

- Do NOT submit the requisition without explicit `CONFIRM`
- On CONFIRM: submit requisition and return the Ariba document number `[Confirmed Fact]`
- On REJECT: log cancellation, notify HR administrator `[Confirmed Fact]`

---

## Non-Functional Requirements

### NFR-1 — Output Labelling
Every output claim must carry exactly one label:
- `[Confirmed Fact]` — data retrieved directly from a system API
- `[Assumption]` — inferred from role/location mapping or business rules
- `[Recommendation]` — suggested action, not yet executed

### NFR-2 — Auditability
All agent actions must be logged with timestamp, employee ID, and action type.

### NFR-3 — Idempotency
If triggered more than once for the same employee ID, the agent must detect existing provisioning and skip (not duplicate) completed steps.

---

## Systems & APIs

| System | Action | Type |
|--------|--------|------|
| SAP SuccessFactors | Read new hire record | Read |
| SAP SuccessFactors | Enrich employee profile | Write |
| SAP S/4HANA Public Cloud | Look up cost center | Read |
| SAP S/4HANA Public Cloud | Assign employee to cost center | Write |
| SAP Ariba | Create equipment purchase requisition | Write — HITL required |

---

## Out of Scope

- IT system account provisioning (Active Directory, email) — separate workflow
- Physical access card programming — handled by Facilities team
- Payroll setup — handled by HR at contract signing
