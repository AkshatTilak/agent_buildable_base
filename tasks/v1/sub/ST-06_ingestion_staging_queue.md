# Subtask: ST-06 — Ingestion Staging Queue & Review APIs

**Parent Link:** [BT-02: Multi-Tier Data Ingestion Engine & Staging Workflows](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/base/BT-02_ingestion_pipeline.md)

## Objective
Implement backend APIs for managing the unified `staged_records` queue, supporting approval/rejection workflows, source provenance audit trails, and atomic promotion of staged records into production tables.

---

## Technical Actionable Steps

- [ ] **Step 1: Build Staging Queue Management APIs**
  - Create endpoints in `ingestion-service`:
    - `GET /ingestion/staging` — Filter by: status (`pending_review`, `approved`, `rejected`), source, provider_type, confidence score range, extraction_method, date range
    - `GET /ingestion/staging/{id}` — Get full staged record with raw_payload, extracted_data, and source provenance
    - `POST /ingestion/staging/{id}/approve` — Promote staged record to production table(s) with atomic transaction
    - `POST /ingestion/staging/{id}/reject` — Reject record with reason, write to audit_logs
    - `POST /ingestion/staging/bulk-approve` — Batch approve records by filter criteria (e.g., all confidence > 0.95 from source X)

- [ ] **Step 2: Implement Record Promotion Logic**
  - On approval, atomically:
    1. Parse `extracted_data` JSONB and map fields to target normalized tables (`providers`, `provider_ratings`, etc.)
    2. Upsert into production tables (match on CCN/NPI/address)
    3. Record `data_source_id` and `ingestion_run_id` on every promoted row
    4. Update `staged_records.review_status` → `approved`, set `reviewed_by` and `reviewed_at`
    5. Write audit log entry

- [ ] **Step 3: Implement `ingestion_runs` Telemetry Logger**
  - Track run metadata per execution:
    - `source_name`, `data_source_id`, `records_fetched`, `records_staged`, `records_auto_upserted`, `records_rejected`
    - `errors_count`, `duration_ms`, `status` (success, failed, partial)
    - `config_snapshot` (JSONB copy of ingestion_config.yaml settings used for this run)

- [ ] **Step 4: Build Source Provenance Query API**
  - `GET /ingestion/provenance/{provider_id}` — Returns full history of all data sources that contributed to a given provider record
  - Shows: which fields came from which source, when, and with what confidence

---

## Dependencies
- Completion of `ST-04C_normalized_schema_design.md` (staged_records and production tables exist)
- Completion of `ST-02_auth_and_rbac_service.md` (role-based access for approve/reject actions)

## Complexity Rating
**High**

## Definition of Done
- Staging endpoints support filtering, approval, rejection, and bulk operations
- Record promotion atomically upserts into normalized production tables with source provenance
- Ingestion runs telemetry logs execution metrics per sync job
- Provenance API shows complete data lineage per provider record
