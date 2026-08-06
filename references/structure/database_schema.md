# Core Database Schema & PostGIS Reference (Normalized)

## Database Technology
- **Database Engine**: PostgreSQL 16 + PostGIS 3.4
- **Vector Search Extension**: `pgvector` (`vector(1536)`)
- **Migration Framework**: Alembic

---

## Schema Design Philosophy
- **Fully Normalized**: Separate tables for deficiencies, penalties, ownership, staffing, quality measures, and surveys
- **Source Provenance**: Every record tracks its data source, extraction method, and confidence score
- **Multi-Source Support**: A single provider can have data from CMS, ombudsman directories, vintage guides, web scraping, and manual entry
- **Master Data Management (MDM)**: Thin Golden Record (`facilities` / `providers`) + Sourced Junction Tables for multi-valued facts (contacts, care types, features, payment options, identifiers)

---

## 1. Core Provider Tables

### `provider_types`
Classification taxonomy for all provider types.
- `id` (SERIAL PK)
- `name` (VARCHAR UNIQUE): nursing_home, hospital, home_health_agency, hospice, inpatient_rehab_facility, long_term_care_hospital, doctor_clinician, assisted_living, independent_living, pace, adult_day, continuing_care

### `providers` / `facilities`
Unified golden provider registry (thin master record).
- `id` (UUID PK)
- `facility_uid` (VARCHAR UNIQUE) — e.g. `FAC-000582`
- `entity_type` (VARCHAR NOT NULL): `facility`, `service_organization`, `practitioner`, `program`
- `name` (VARCHAR NOT NULL)
- `provider_type_id` (FK → `provider_types.id`)
- `cms_ccn` (VARCHAR, UNIQUE, NULLABLE) — CMS Certification Number
- `npi` (VARCHAR, UNIQUE, NULLABLE) — National Provider Identifier
- `federal_provider_number` (VARCHAR, NULLABLE)
- `location` (GEOGRAPHY(POINT, 4326))
- `address_line_1`, `address_line_2` (VARCHAR)
- `city`, `state`, `zip`, `county` (VARCHAR)
- `phone`, `fax` (VARCHAR)
- `website` (VARCHAR)
- `ownership_type` (VARCHAR: for_profit, nonprofit, government)
- `bed_count` (INTEGER, NULLABLE)
- `status` (ENUM: unclaimed, claimed, pending_verification, suspended)
- `is_active` (BOOLEAN DEFAULT TRUE)
- `created_at`, `updated_at`, `deleted_at` (TIMESTAMP)
- `extended_attributes` (JSONB) — type-specific fields
- `embedding` (vector(1536))

**Indexes:**
- GIST on `location`
- B-tree on `cms_ccn`, `npi`, `federal_provider_number`
- GIN on `extended_attributes`

---

## 2. Master Data Management (MDM) Sourced Junction Tables

### `facility_care_types`
Multi-valued care types per facility.
- `id` (UUID PK)
- `facility_uid` (VARCHAR FK → `providers.facility_uid`)
- `care_type_code` (VARCHAR FK → `taxonomy_care_types.code`)
- `source_system` (VARCHAR)
- `raw_category` (VARCHAR)
- `is_active` (BOOLEAN DEFAULT TRUE)

### `facility_features`
Multi-valued contextual features and amenities per facility.
- `id` (UUID PK)
- `facility_uid` (VARCHAR FK → `providers.facility_uid`)
- `feature_code` (VARCHAR FK → `taxonomy_features.code`)
- `feature_value` (VARCHAR) — `Included`, `Available`, `Extra Cost`, `Partially Included`, `Community Facilities & W/D Provided`
- `source_system` (VARCHAR)
- `raw_key` (VARCHAR)
- `is_active` (BOOLEAN DEFAULT TRUE)

**Indexes:**
- B-tree on `feature_code` (`idx_features_code`) for instant amenity filtering
- B-tree on `facility_uid` (`idx_features_facility`) for facility detail hydration

### `facility_payment_options`
Multiple accepted payment methods per facility.
- `id` (UUID PK)
- `facility_uid` (VARCHAR FK → `providers.facility_uid`)
- `payment_option_code` (VARCHAR FK → `taxonomy_payment_options.code`)
- `source_system` (VARCHAR)
- `raw_value` (VARCHAR)
- `is_active` (BOOLEAN DEFAULT TRUE)

### `facility_contacts`
Multiple contact details per facility (primary/secondary phones, faxes, websites, administrators).
- `id` (UUID PK)
- `facility_uid` (VARCHAR FK → `providers.facility_uid`)
- `contact_type` (VARCHAR): `phone`, `fax`, `website`, `administrator`
- `contact_value` (VARCHAR)
- `is_primary` (BOOLEAN DEFAULT FALSE)
- `source_system` (VARCHAR)
- `is_active` (BOOLEAN DEFAULT TRUE)

### `facility_identifiers`
Multiple registration authority IDs (`CCN`, `state_license_id`).
- `id` (UUID PK)
- `facility_uid` (VARCHAR FK → `providers.facility_uid`)
- `id_type` (VARCHAR): `cms_ccn`, `state_license_id`, `npi`
- `id_value` (VARCHAR)
- `source_system` (VARCHAR)

### `facility_source_links`
Full source provenance link connecting golden records back to raw extracted JSON payloads.
- `id` (UUID PK)
- `facility_uid` (VARCHAR FK → `providers.facility_uid`)
- `source_system` (VARCHAR): `cms_nursing_home_provider_info`, `ombudsman_pdf`, `vintage_guide`
- `source_record_id` (VARCHAR)
- `match_method` (VARCHAR): `single_source`, `auto_link`, `manual_approval`
- `confidence_score` (FLOAT)
- `raw_payload` (JSONB)

---

## 3. Quality & Ratings Tables

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
- `measure_value` (VARCHAR)
- `national_average` (VARCHAR, NULLABLE)
- `state_average` (VARCHAR, NULLABLE)
- `data_source_id` (UUID FK → `data_sources.id`)
- `reporting_period_start`, `reporting_period_end` (DATE)

### `provider_surveys`
Patient experience survey results (CAHPS, HCAHPS, HHCAHPS).
- `id` (UUID PK)
- `provider_id` (UUID FK)
- `survey_type` (VARCHAR)
- `measure_code`, `measure_description` (VARCHAR)
- `provider_score` (VARCHAR)
- `state_avg`, `national_avg` (VARCHAR, NULLABLE)
- `response_count` (INTEGER, NULLABLE)
- `data_source_id` (UUID FK)
- `survey_period` (VARCHAR)

---

## 4. Deficiency & Penalty Tables

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

## 5. Ownership & Staffing Tables

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

## 6. Source Provenance & Ingestion Tables

### `data_sources`
Registry of all data sources feeding the platform.
- `id` (UUID PK)
- `source_name` (VARCHAR UNIQUE)
- `source_type` (ENUM: api, pdf_tabular, pdf_ocr, web_scrape, manual)
- `source_url` (TEXT, NULLABLE)
- `data_dictionary_url` (TEXT, NULLABLE)
- `last_sync_at` (TIMESTAMP, NULLABLE)
- `sync_schedule` (VARCHAR, NULLABLE)

### `staged_records`
Universal staging table for all extraction and preprocessing pipelines.
- `id` (UUID PK)
- `data_source_id` (UUID FK → `data_sources.id`)
- `target_table` (VARCHAR)
- `raw_payload` (JSONB)
- `extracted_data` (JSONB)
- `confidence_score` (FLOAT)
- `extraction_method` (VARCHAR: cms_api, precision_vintage_pdf, precision_ombudsman_pdf, gemini_llm, manual)
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

## 7. Audit & Access Logs

### `audit_logs`
Immutable compliance and access log.
- `id` (UUID PK)
- `actor_id` (UUID FK → `users.id`)
- `action` (VARCHAR)
- `target_resource` (VARCHAR)
- `payload` (JSONB)
- `timestamp` (TIMESTAMP)
