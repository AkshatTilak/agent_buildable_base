# Subtask: ST-13 — Internal Admin & ERP Dashboard (Next.js / React)

**Parent Link:** [BT-04: Client Applications & Shared Mobile Shell](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/base/BT-04_client_surfaces.md)

## Objective
Build the Next.js internal administration dashboard (`apps/web-admin/`) for platform operations, compliance officers, and data admins to manage human-in-the-loop verification queues, audit logs, and ingestion pipelines.

---

## Technical Actionable Steps

- [ ] **Step 1: Bootstrap App Shell & Super Admin Guard**
  - Next.js setup restricted to internal platform roles (`platform_data_admin`, `platform_compliance_admin`, `platform_super_admin`, `platform_support`).

- [ ] **Step 2: Build Human-in-the-Loop Staging & AI Verification Queue UI**
  - Review dashboard displaying staged PDF/OCR items and low-confidence AI extraction outputs.
  - Side-by-side diff viewer showing original source text vs. extracted JSON fields with inline editing and Approve/Reject controls.

- [ ] **Step 3: Build Ingestion Pipeline Health Dashboard**
  - Live charts for `ingestion_runs`: ETL DAG execution history, records fetched/staged, error rates, and pipeline staleness alerts.

- [ ] **Step 4: Build Compliance & Audit Log Viewer**
  - Immutable audit trail explorer for user impersonation, role updates, and Stark/AKS compliance routing logs.

---

## Dependencies
- Completion of `ST-06_ingestion_staging_queue.md` and `ST-09_human_in_loop_review_queue.md`.

## Complexity Rating
**High**

## Definition of Done
- `web-admin` permits data admins to review, edit, and approve staged OCR/PDF records and AI outputs.
- Audit logs and ingestion run telemetry display accurately.
