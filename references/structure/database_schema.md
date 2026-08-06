# Core Database Schema & PostGIS Reference (Normalized)

## Database Technology
- **Database Engine**: PostgreSQL 16 + PostGIS 3.4
- **Vector Search Extension**: `pgvector` (`vector(1536)`)
- **Migration Framework**: Alembic

---

## Schema Design Philosophy
- **Fully Normalized**: Separate tables for deficiencies, penalties, ownership, staffing, quality measures, and surveys
- **Source Provenance**: Every record tracks its data source, extraction method, and confidence score
- **Multi-Source Support**: A single provider can have ratings/data from CMS, ombudsman directories, vintage guides, web scraping, and manual entry
- **Provider-Type Flexibility**: JSONB `extended_attributes` column for type-specific fields not worth their own table

---

## 1. Core Provider Tables

### `provider_types`
Classification taxonomy for all provider types.
- `id` (SERIAL PK)
- `name` (VARCHAR UNIQUE): nursing_home, hospital, home_health_agency, hospice, inpatient_rehab_facility, long_term_care_hospital, doctor_clinician, assisted_living, independent_living, pace, adult_day, continuing_care

### `providers`
Unified provider registry (replaces the flat `facilities` table).
- `id` (UUID PK)
- `name` (VARCHAR NOT NULL)
- `provider_type_id` (FK → `provider_types.id`)
- `cms_ccn` (VARCHAR, UNIQUE, NULLABLE) — CMS Certification Number
- `npi` (VARCHAR, UNIQUE, NULLABLE) — National Provider Identifier
- `federal_provider_number` (VARCHAR, NULLABLE)
- `location` (GEOGRAPHY(POINT, 4326))
- `address_line_1`, `address_line_2` (VARCHAR)
- `city`, `state`, `zip`, `county` (VARCHAR)
- `phone`, `fax` (VARCHAR)
- `ownership_type` (VARCHAR: individual, corporation, government, nonprofit, LLC)
- `bed_count` (INTEGER, NULLABLE)
- `status` (ENUM: unclaimed, claimed, pending_verification, suspended)
- `extended_attributes` (JSONB) — type-specific fields
- `embedding` (vector(1536))
- `created_at`, `updated_at` (TIMESTAMP)

**Indexes:**
- GIST on `location`
- B-tree on `cms_ccn`, `npi`, `federal_provider_number`
- GIN on `extended_attributes`

---

## 2. Quality & Ratings Tables

### `provider_ratings`
Time-series official ratings from multiple sources.
- `id` (UUID PK)
- `provider_id` (UUID FK → `providers.id`)
- `source` (ENUM: cms, ombudsman, internal, user, vintage_guide, web_scrape)
- `source_dataset_id` (VARCHAR) — e.g., CMS dataset ID `4pq5-n9py`
- `data_source_id` (UUID FK → `data_sources.id`)
- `rating_type` (VARCHAR: overall, health_inspection, staffing, quality_measure, fire_safety)
- `rating_value` (NUMERIC)
- `rating_max` (NUMERIC)
- `as_of_date` (DATE)
- `created_at` (TIMESTAMP)

### `provider_quality_measures`
Granular quality measure scores per provider.
- `id` (UUID PK)
- `provider_id` (UUID FK)
- `measure_code` (VARCHAR)
- `measure_name` (VARCHAR)
- `measure_value` (VARCHAR) — text to handle both numeric and categorical measures
- `national_average` (VARCHAR, NULLABLE)
- `state_average` (VARCHAR, NULLABLE)
- `data_source_id` (UUID FK → `data_sources.id`)
- `reporting_period_start`, `reporting_period_end` (DATE)

### `provider_surveys`
Patient experience survey results (CAHPS, HCAHPS, HHCAHPS, ICH-CAHPS).
- `id` (UUID PK)
- `provider_id` (UUID FK)
- `survey_type` (VARCHAR: hcahps, cahps_hospice, hhcahps, ich_cahps, oas_cahps)
- `measure_code`, `measure_description` (VARCHAR)
- `provider_score` (VARCHAR)
- `state_avg`, `national_avg` (VARCHAR, NULLABLE)
- `response_count` (INTEGER, NULLABLE)
- `data_source_id` (UUID FK)
- `survey_period` (VARCHAR)

---

## 3. Deficiency & Penalty Tables

### `provider_deficiencies`
Health inspection and fire safety citations.
- `id` (UUID PK)
- `provider_id` (UUID FK)
- `deficiency_type` (ENUM: health, fire_safety)
- `survey_date` (DATE)
- `citation_code` (VARCHAR)
- `deficiency_description` (TEXT)
- `scope_severity` (VARCHAR)
- `correction_date` (DATE, NULLABLE)
- `data_source_id` (UUID FK)

### `provider_penalties`
Fines, payment denials, and enforcement actions.
- `id` (UUID PK)
- `provider_id` (UUID FK)
- `penalty_type` (ENUM: fine, payment_denial, suspension, other)
- `penalty_amount` (NUMERIC, NULLABLE)
- `penalty_date` (DATE)
- `reason` (TEXT)
- `data_source_id` (UUID FK)

---

## 4. Ownership & Staffing Tables

### `provider_ownership`
Ownership chain — multiple owners per provider.
- `id` (UUID PK)
- `provider_id` (UUID FK)
- `owner_name` (VARCHAR)
- `owner_type` (ENUM: individual, corporation, government, nonprofit, llc)
- `ownership_percentage` (NUMERIC, NULLABLE)
- `role` (VARCHAR: managing_employee, officer, director, owner, managing_member)
- `association_date` (DATE, NULLABLE)
- `data_source_id` (UUID FK)

### `provider_staffing`
Staffing ratios over time.
- `id` (UUID PK)
- `provider_id` (UUID FK)
- `staff_type` (VARCHAR: rn, lpn, cna, pt, ot, slp, aide, physician, total)
- `hours_per_resident_per_day` (NUMERIC, NULLABLE)
- `total_staff_hours` (NUMERIC, NULLABLE)
- `reported_staff_count` (INTEGER, NULLABLE)
- `reporting_period` (VARCHAR)
- `data_source_id` (UUID FK)

---

## 5. Source Provenance & Ingestion Tables

### `data_sources`
Registry of all data sources feeding the platform.
- `id` (UUID PK)
- `source_name` (VARCHAR UNIQUE): e.g., `cms_nursing_homes_provider_info`, `incog_ombudsman_2024`, `vintage_iowa_2024`
- `source_type` (ENUM: api, pdf_tabular, pdf_ocr, web_scrape, manual)
- `source_url` (TEXT, NULLABLE)
- `data_dictionary_url` (TEXT, NULLABLE)
- `last_sync_at` (TIMESTAMP, NULLABLE)
- `sync_schedule` (VARCHAR, NULLABLE): nightly, weekly, monthly, on_demand

### `staged_records`
Universal staging table for all extraction pipelines.
- `id` (UUID PK)
- `data_source_id` (UUID FK → `data_sources.id`)
- `target_table` (VARCHAR): which production table this record promotes to
- `raw_payload` (JSONB)
- `extracted_data` (JSONB)
- `confidence_score` (FLOAT)
- `extraction_method` (VARCHAR: cms_api, pdfplumber, paddleocr, gemini_llm, manual)
- `review_status` (ENUM: pending_review, approved, rejected, auto_approved)
- `reviewed_by` (UUID FK → `users.id`, NULLABLE)
- `review_notes` (TEXT, NULLABLE)
- `created_at`, `reviewed_at` (TIMESTAMP)

### `ingestion_runs`
ETL execution telemetry.
- `id` (UUID PK)
- `data_source_id` (UUID FK → `data_sources.id`)
- `source_name` (VARCHAR)
- `records_fetched` (INTEGER)
- `records_staged` (INTEGER)
- `records_upserted` (INTEGER)
- `errors_count` (INTEGER)
- `duration_ms` (INTEGER)
- `status` (VARCHAR: success, failed, partial)
- `config_snapshot` (JSONB)
- `started_at`, `completed_at` (TIMESTAMP)

---

## 6. Audit & Access Logs

### `audit_logs`
Immutable compliance and access log.
- `id` (UUID PK)
- `actor_id` (UUID FK → `users.id`)
- `action` (VARCHAR)
- `target_resource` (VARCHAR)
- `payload` (JSONB)
- `timestamp` (TIMESTAMP)
