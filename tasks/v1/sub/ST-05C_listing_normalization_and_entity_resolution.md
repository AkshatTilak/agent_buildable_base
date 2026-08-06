# Subtask: ST-05C — Listing Preprocessing, Classification & Entity Resolution

**Parent Link:** [BT-02: Multi-Tier Data Ingestion Engine & Staging Workflows](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/base/BT-02_ingestion_pipeline.md)
**Reference Rules:** [ingestion_extraction_rules.md](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/references/logic/ingestion_extraction_rules.md)

## Objective
Build a deterministic listing preprocessing and entity resolution engine (`scripts/preprocess_listings.py`) that sits between raw extractions (`ST-05A`, `ST-05B`, `ST-04D`) and the staging review queue (`ST-06`). The engine reconciles multi-source listings across CMS, INCOG Ombudsman, and Vintage Guide data into a normalized Master Data Management (MDM) Golden Record schema with source-attributed junction tables and automated deduplication scoring.

---

## Technical Actionable Steps

- [x] **Step 1: Define Canonical Taxonomies & Crosswalk YAMLs**
  - Create human-editable, diffable YAML taxonomies under `config/taxonomy/`:
    - `care_types.yaml`: Canonical care type codes mapped to `entity_type` (`facility`, `service_organization`, `practitioner`, `program`).
    - `features.yaml`: ~65 canonical feature/amenity codes with value domains (`Included`, `Available`, `Partially Included`, `Extra Cost`).
    - `payment_and_ownership.yaml`: Controlled payment option codes and normalized ownership classification rules (`for_profit`, `nonprofit`, `government`).

- [x] **Step 2: Implement Singular Facts vs. Multi-Valued Fact Architecture**
  - **Golden Record Table (`facilities`)**: Singular core identity (`facility_uid`, `entity_type`, `primary_name`, canonical address/phone, `status`, `is_active`, `created_at`, `updated_at`, `deleted_at`).
  - **Sourced Junction Tables**:
    - `facility_care_types`: Multi-valued care type classifications per facility (`nursing_home`, `assisted_living`).
    - `facility_features`: Indexed amenity assignments with contextual values.
    - `facility_payment_options`: Multiple accepted payment methods (`Private Pay`, `Medicaid`, `Medicare`, `Housing Choice Voucher`).
    - `facility_contacts`: Multi-contact management (primary/secondary phones, faxes, websites, administrator names).
    - `facility_identifiers`: Multiple registration authority IDs (`CCN`, `state_license_id`).
    - `facility_ownership`: Detailed owner names, roles, and ownership percentages.
    - `facility_source_links`: Provenance links connecting golden records back to raw source JSON payloads.

- [x] **Step 3: Implement Deduplication, Similarity Scoring & Dual Guard Rails**
  - **ZIP/City Blocking**: Candidate pair comparison scoped within 5-digit ZIP codes (or state/city fallback) to prevent $O(n^2)$ full scans.
  - **Weighted Similarity Matcher**: Blends normalized string similarity across Name, Address, and Phone numbers.
  - **Confidence Thresholds**:
    - $\ge 0.80$: Automated link into a single golden facility.
    - $0.60 - 0.79$: Flagged for human review in `match_review_queue.csv`.
  - **Dual Guard Rail Protection**: Prevents false auto-merges on shared marketing/campus phone numbers when street addresses disagree.

- [x] **Step 4: Implement Lifecycle Management, Soft Delete & Audit Logging**
  - **Fact-Level Enable/Disable**: `is_active` flags on `facility_features`, `facility_care_types`, and `facilities` allow toggling individual facts without data loss.
  - **Soft Delete**: `deleted_at` timestamp preserves full historical lineage.
  - **Audit Logging**: Appends every pipeline action (`FACILITY_CREATED`, `LINK_AUTO`, `REVIEW_QUEUED`) to `audit_log.csv` and `ingestion_run_summary.json`.

- [x] **Step 5: Fast Search Indexing & Staging Queue Integration**
  - Generate B-tree indexes (`idx_features_code`, `idx_features_facility`, `idx_care_types_code`, `idx_care_types_facility`) for high-speed feature filtering.
  - Load preprocessed golden records into `staged_records` review queue (`ST-06`) with confidence scores and source provenance.

---

## Dependencies
- Completion of `ST-05A`, `ST-05B`, and `ST-04D` raw extractions.
- Libraries: `pyyaml`, `sqlite3`, `pytest`

## Complexity Rating
**Very High**

## Definition of Done
- Preprocessing engine executes deterministically via `python3 scripts/preprocess_listings.py`.
- Raw candidates resolved into golden facilities with multi-source auto-linking.
- Candidate pairs below confidence threshold flagged in `match_review_queue.csv` and unmapped raw terms in `unmapped_terms_review.csv`.
- Staging queue loader inserts golden records into `staged_records` for human approval (`ST-06`).
- All workspace pytest unit tests passing.
