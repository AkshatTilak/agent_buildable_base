# Subtask: ST-06B — Prefect DAG Orchestration & Scheduling

**Parent Link:** [BT-02: Multi-Tier Data Ingestion Engine & Staging Workflows](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/base/BT-02_ingestion_pipeline.md)

## Objective
Define and implement Prefect ETL orchestration DAGs for all CMS sync jobs, PDF ingestion triggers, and health monitoring digests. All schedules are driven by the user-selectable `ingestion_config.yaml`.

---

## Technical Actionable Steps

- [ ] **Step 1: Define CMS Sync DAGs (Per-Theme)**
  - `cms_nursing_homes_sync` — Nightly for priority_1, weekly for priority_2 datasets
  - `cms_hospitals_sync` — Nightly for general info, weekly for quality measures
  - `cms_home_health_sync` — Nightly
  - `cms_hospice_sync` — Nightly
  - `cms_doctors_clinicians_sync` — Weekly
  - `cms_inpatient_rehab_sync` — Weekly
  - `cms_long_term_care_sync` — Weekly
  - Each DAG reads `ingestion_config.yaml` for enabled datasets and schedule overrides

- [ ] **Step 2: Define NPI Registry Reconciliation DAG**
  - `npi_registry_reconciliation` — Weekly
  - Cross-references CCN and NPI numbers across all synced providers

- [ ] **Step 3: Define PDF Ingestion Trigger DAGs**
  - `ombudsman_pdf_ingest` — On-demand (triggered when new PDFs appear in `data/raw_sources/ombudsman_pdfs/`)
  - `vintage_magazine_ocr_ingest` — On-demand (triggered when new PDFs appear in `data/raw_sources/vintage_pdfs/`)

- [ ] **Step 4: Define Health Monitoring DAGs**
  - `ingestion_health_digest` — Daily
    - Computes: pipeline success rates, dead-letter counts, staleness metrics, data freshness alerts
    - Alerts if any CMS dataset hasn't been synced within its expected window

- [ ] **Step 5: Implement Prefect Infrastructure**
  - Prefect server deployment config (local or Prefect Cloud)
  - Worker pool configuration
  - Celery/Redis task queue for async job dispatch

---

## Dependencies
- Completion of `ST-04D_ingestion_tier1_cms_api.md` (CMS sync jobs to orchestrate)
- Completion of `ST-05A_vintage_magazine_ocr_pipeline.md` and `ST-05B_ombudsman_pdf_extraction.md` (PDF pipelines)
- Libraries: `prefect`, `celery`, `redis`

## Complexity Rating
**High**

## Definition of Done
- All Prefect DAGs defined and registered
- CMS sync DAGs execute on schedule and respect `ingestion_config.yaml` settings
- PDF ingestion DAGs trigger on new file detection
- Health digest produces daily staleness and success rate reports
- Ingestion run telemetry flows into `ingestion_runs` table
