# Subtask: ST-12 — B2B Facility Management Hub (Next.js / React)

**Parent Link:** [BT-04: Client Applications & Shared Mobile Shell](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/base/BT-04_client_surfaces.md)

## Objective
Build the Next.js B2B SaaS dashboard (`apps/web-facility/`) for facility administrators and admissions teams to claim listings, update clinical/pricing data, manage incoming family leads via Kanban, and schedule tours.

---

## Technical Actionable Steps

- [ ] **Step 1: Bootstrap App Shell & RBAC Authentication Guard**
  - Next.js setup with Auth middleware enforcing facility staff roles (`facility_staff_clinical`, `facility_admissions`, `facility_owner_admin`).

- [ ] **Step 2: Build Facility Profile Claim & Listing Editor**
  - Verification workflow for claiming existing CMS directory listings.
  - Editor forms for updating clinical capabilities, nurse-to-resident ratios, room pricing tiers, and amenities.

- [ ] **Step 3: Build CRM Lead Management Kanban Board**
  - Interactive lead management board (New, Contacted, Tour Scheduled, Placed, Closed).
  - Messaging inbox for family inquiries and tour scheduling calendar view.

- [ ] **Step 4: Build Team & Org Management Settings**
  - User role assignment for facility staff members and billing subscription settings.

---

## Dependencies
- Completion of `ST-02_auth_and_rbac_service.md` and `ST-10_leads_and_billing_service.md`.

## Complexity Rating
**High**

## Definition of Done
- `web-facility` application authenticates scoped facility users.
- Admissions staff can move family leads across CRM kanban stages and update facility profile data.
