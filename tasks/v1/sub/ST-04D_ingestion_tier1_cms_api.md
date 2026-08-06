# Subtask: ST-04D — Tier 1 CMS API Ingestion Pipelines

**Parent Link:** [BT-02: Multi-Tier Data Ingestion Engine & Staging Workflows](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/base/BT-02_ingestion_pipeline.md)

*Replaces the original `ST-04_ingestion_tier1_cms_api.md` — expanded from nursing-homes-only to all 7 CMS provider themes.*

## Objective
Implement per-theme CMS API sync jobs within `services/ingestion-service/` that pull data across all 7 provider themes via the CMS Provider Data API, transform it to match the normalized schema, and upsert into production tables. Phase 1 focuses on: **general info, locations/names, reviews/penalties, ownership**.

---

## Technical Actionable Steps

- [x] **Step 1: Implement CMS Provider Data API Client**
  - Build async REST client for CMS Provider Data API v1
  - Endpoints:
    - Metastore catalog: `GET /api/1/metastore/schemas/dataset/items`
    - Dataset query: `GET /api/1/datastore/query/{dataset_id}` (supports pagination, filtering)
    - Direct CSV download: `GET /provider-data/sites/default/files/resources/{file_hash}/{filename}.csv`
  - Implement rate limiting, retry with exponential backoff, and pagination handling

- [x] **Step 2: Build User-Selectable Dataset Configuration Loader**
  - Read `data/ingestion_config.yaml` to determine which datasets are enabled
  - Validate dataset IDs against live metastore catalog
  - Log warnings for stale/removed datasets

- [x] **Step 3: Implement Per-Theme Sync Job Architecture**
  - Generic `CMSThemeSyncJob` base class with:
    - `theme_name`, `enabled_datasets`, `field_mapping_config`
    - `fetch()` → download CSV or query API
    - `transform()` → map CMS columns to TrueCare normalized schema
    - `load()` → upsert into appropriate tables with source provenance
  - Theme-specific subclasses:
    - `NursingHomeSyncJob` — maps to: `providers`, `provider_ratings`, `provider_deficiencies`, `provider_penalties`, `provider_ownership`, `provider_staffing`
    - `HospitalSyncJob` — maps to: `providers`, `provider_ratings`, `provider_quality_measures`, `provider_surveys`
    - `HomeHealthSyncJob` — maps to: `providers`, `provider_ratings`, `provider_quality_measures`
    - `HospiceSyncJob` — maps to: `providers`, `provider_ratings`, `provider_surveys`
    - `DoctorClinicianSyncJob` — maps to: `providers`, `provider_quality_measures`
    - `InpatientRehabSyncJob` — maps to: `providers`, `provider_ratings`, `provider_quality_measures`
    - `LongTermCareSyncJob` — maps to: `providers`, `provider_ratings`, `provider_quality_measures`

- [x] **Step 4: Implement Cross-Dataset Provider Reconciliation**
  - Match providers across datasets using CCN, NPI, Federal Provider Number
  - Detect duplicates and merge records (same facility appearing in multiple themes)
  - Log reconciliation conflicts for manual review

- [x] **Step 5: Implement Source Provenance on Every Upsert**
  - Every inserted/updated row records:
    - `data_source_id` (FK → `data_sources`)
    - `source_dataset_id` (CMS dataset identifier, e.g., `4pq5-n9py`)
    - `ingestion_run_id` (FK → `ingestion_runs`)
    - `extracted_at` (timestamp of this sync run)

- [x] **Step 6: Implement Phase 1 Priority Sync Jobs**
  - Wire up and test sync for Phase 1 datasets:
    - Nursing Homes: Provider Information (`4pq5-n9py`), Ownership (`y2hd-n93e`), Penalties (`g6vv-ecav`), Health Deficiencies (`r5ix-sfxw`)
    - Hospitals: Hospital General Information (`xubh-q36u`)
    - Home Health: Home Health Care Agencies (`6jpm-sxkc`)
    - Hospice: Hospice - General Information (`yc9t-dgbk`), Hospice - Provider Data (`252m-zfp9`)
    - Doctors/Clinicians: National Downloadable File (`mj5m-pzi6`)
    - Inpatient Rehab: General Information (`jkbs-gcrp`)
    - Long-Term Care: General Information (`azum-44iv`)

---

## Dependencies
- Completion of `ST-04C_normalized_schema_design.md` (target schema must exist)
- Completion of `ST-04B_cms_dataset_research.md` (field mappings and dataset config)

## Complexity Rating
**Very High**

## Definition of Done
- CMS API client handles pagination, rate limiting, and retries
- Phase 1 sync jobs successfully populate `providers`, `provider_ratings`, `provider_deficiencies`, `provider_penalties`, `provider_ownership` across all 7 themes
- Every row has source provenance (data_source_id, source_dataset_id, ingestion_run_id)
- User-selectable config enables toggling datasets on/off without code changes
- Test suite validates field mapping accuracy and conflict resolution
