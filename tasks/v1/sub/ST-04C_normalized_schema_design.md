# Subtask: ST-04C — Normalized Database Schema Design

**Parent Link:** [BT-02: Multi-Tier Data Ingestion Engine & Staging Workflows](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/base/BT-02_ingestion_pipeline.md)

## Objective
Design and implement a fully normalized PostgreSQL + PostGIS database schema that can accommodate data from all 7 CMS provider themes, vintage magazine extractions, and ombudsman directory records. Every record must track its data source, extraction timestamp, and confidence score. The schema must support multiple review sources per facility.

---

## Technical Actionable Steps

- [x] **Step 1: Design Core Provider Tables**
  - `providers` — Unified provider registry (replaces the flat `facilities` table)
    - `id` (UUID PK), `name`, `provider_type` (FK → `provider_types`), `cms_ccn`, `npi`, `federal_provider_number`
    - `location` (GEOGRAPHY POINT 4326), `address_line_1`, `address_line_2`, `city`, `state`, `zip`, `county`, `phone`, `fax`
    - `ownership_type`, `bed_count`, `status` (ENUM: unclaimed, claimed, pending_verification, suspended)
    - `embedding` (vector(1536))
    - `created_at`, `updated_at`
  - `provider_types` — Taxonomy for provider classification
    - Nursing Home, Hospital, Home Health Agency, Hospice, Inpatient Rehab Facility, Long-Term Care Hospital, Doctor/Clinician, Assisted Living, Independent Living, PACE, Adult Day, etc.

- [x] **Step 2: Design Quality & Ratings Tables**
  - `provider_ratings` — Time-series official ratings with source tracking
    - `id`, `provider_id` (FK), `source` (ENUM: cms, ombudsman, internal, user, vintage_guide), `source_dataset_id`
    - `rating_type` (overall, health_inspection, staffing, quality_measure, fire_safety)
    - `rating_value` (NUMERIC), `rating_max` (NUMERIC), `as_of_date`, `created_at`
  - `provider_quality_measures` — Granular quality measure scores
    - `id`, `provider_id` (FK), `measure_code`, `measure_name`, `measure_value`, `national_average`, `state_average`
    - `data_source`, `reporting_period_start`, `reporting_period_end`
  - `provider_surveys` — Patient experience survey results (CAHPS, HCAHPS, ICH-CAHPS)
    - `id`, `provider_id` (FK), `survey_type`, `measure_code`, `measure_description`
    - `provider_score`, `state_avg`, `national_avg`, `response_count`
    - `data_source`, `survey_period`

- [x] **Step 3: Design Deficiency & Penalty Tables**
  - `provider_deficiencies` — Health inspection and fire safety citations
    - `id`, `provider_id` (FK), `deficiency_type` (ENUM: health, fire_safety)
    - `survey_date`, `citation_code`, `deficiency_description`, `scope_severity`
    - `correction_date`, `data_source`
  - `provider_penalties` — Fines, payment denials, suspensions
    - `id`, `provider_id` (FK), `penalty_type` (ENUM: fine, payment_denial, suspension, other)
    - `penalty_amount` (NUMERIC), `penalty_date`, `reason`
    - `data_source`

- [x] **Step 4: Design Ownership & Staffing Tables**
  - `provider_ownership` — Ownership chain (multiple owners per facility)
    - `id`, `provider_id` (FK), `owner_name`, `owner_type` (individual, corporation, government, nonprofit)
    - `ownership_percentage`, `role` (managing_employee, officer, director, owner)
    - `association_date`, `data_source`
  - `provider_staffing` — Nurse/aide staffing ratios over time
    - `id`, `provider_id` (FK), `staff_type` (RN, LPN, CNA, PT, OT, SLP, aide)
    - `hours_per_resident_per_day` (NUMERIC), `total_staff_hours`, `reported_staff_count`
    - `reporting_period`, `data_source`

- [x] **Step 5: Design Source Provenance & Staging Tables**
  - `data_sources` — Registry of all data sources
    - `id`, `source_name` (cms_nursing_homes, cms_hospitals, incog_ombudsman, vintage_iowa_2024, ...)
    - `source_type` (ENUM: api, pdf_tabular, pdf_ocr, web_scrape, manual)
    - `source_url`, `data_dictionary_url`, `last_sync_at`, `sync_schedule`
  - `staged_records` — Universal staging table (replaces `staged_facilities`)
    - `id`, `data_source_id` (FK), `target_table`, `raw_payload` (JSONB)
    - `extracted_data` (JSONB), `confidence_score`, `extraction_method`
    - `review_status` (ENUM: pending_review, approved, rejected, auto_approved)
    - `reviewed_by` (FK → users), `review_notes`, `created_at`, `reviewed_at`
  - `ingestion_runs` — ETL execution telemetry (unchanged from original)

- [x] **Step 6: Design Provider-Type-Specific JSONB Extension Fields**
  - Add `extended_attributes` (JSONB) column to `providers` for fields unique to specific provider types that don't warrant their own tables:
    - Nursing Homes: continuing_care_retirement, special_focus_facility, resident_council, family_council
    - Hospitals: emergency_services, meets_meaningful_use, hospital_type (acute, critical_access, VA)
    - Home Health: offers_nursing, offers_pt, offers_ot, offers_speech, offers_medical_social
    - Hospice: hospice_type (freestanding, hospital_based, home_health_based)

- [x] **Step 7: Write Alembic Migrations**
  - Create migration files for all new tables
  - Migrate from old flat `facilities` to new `providers` schema
  - Add proper indexes: spatial (GIST on location), B-tree on cms_ccn/npi, GIN on JSONB

---

## Dependencies
- Completion of `ST-04B_cms_dataset_research.md` (field mappings and dataset analysis)
- Completion of `ST-01_monorepo_and_db_scaffold.md` (Alembic and DB infrastructure)

## Complexity Rating
**Very High**

## Definition of Done
- All tables created via Alembic migrations, `alembic upgrade head` succeeds
- Every table has a `data_source` or `data_source_id` reference for provenance
- Multiple review/rating sources per provider are supported (CMS is not the only source)
- Schema supports all 7 CMS provider themes with type-specific JSONB extensions
- Schema documentation updated in [database_schema.md](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/references/structure/database_schema.md)
