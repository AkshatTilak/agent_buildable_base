# Subtask: ST-10 — Leads & AKS-Compliant Billing Services

**Parent Link:** [BT-03: Hybrid Search Engine & AI Agent Subsystem](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/base/BT-03_search_ai_engine.md)

## Objective
Implement `services/leads-service/` for CRM lead state management and tour booking, alongside `services/billing-service/` for Stark Law / Anti-Kickback Statute (AKS) compliant monetization firewall logic.

---

## Technical Actionable Steps

- [ ] **Step 1: Implement `leads-service` CRM Backend**
  - CRUD operations for family inquiry leads (`new`, `contacted`, `tour_scheduled`, `tour_completed`, `placed`, `closed`).
  - Tour scheduling calendar booking endpoints with email notification dispatch.

- [ ] **Step 2: Implement AKS / Stark Compliance Engine in `billing-service`**
  - Implement compliance firewall logic: restrict lead generation billing models to flat subscription fees / software SaaS licensing rather than per-patient referral bounties (preventing federal AKS healthcare violations).
  - Add compliance check middleware for lead routing requests (`platform_compliance_admin` audit logging).

- [ ] **Step 3: Build Billing & Subscription Management Endpoints**
  - Facility SaaS subscription management, billing status, invoice generation, and tier access control.

---

## Dependencies
- Completion of `ST-01_monorepo_and_db_scaffold.md` and `ST-02_auth_and_rbac_service.md`.

## Complexity Rating
**High**

## Definition of Done
- Leads transition cleanly across CRM states and allow tour booking scheduling.
- Billing service enforces flat SaaS / non-referral fee structures and generates compliance logs.
