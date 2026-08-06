# Subtask: ST-04B — CMS Dataset Research & Schema Analysis

**Parent Link:** [BT-02: Multi-Tier Data Ingestion Engine & Staging Workflows](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/base/BT-02_ingestion_pipeline.md)

## Objective
Systematically analyze all 130+ CMS Provider Data datasets across 7 themes. Produce a structured dataset catalog with priority tiers, field mappings, cross-dataset join keys, and a user-selectable configuration system that lets the user toggle individual datasets on/off for ingestion.

---

## Technical Actionable Steps

- [x] **Step 1: Build CMS Dataset Catalog Reference**
  - Use CMS Provider Data API (`/api/1/metastore/schemas/dataset/items`) to pull full dataset metadata
  - For each dataset, record: title, identifier, theme, description, CSV download URL, data dictionary URL, update frequency, last modified date
  - Organize catalog by the 7 target themes (see [cms_dataset_catalog.md](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/references/resource/cms_dataset_catalog.md))

- [x] **Step 2: Identify Phase 1 Priority Datasets**
  - Phase 1 criteria (per user direction): general info, locations/names, reviews/penalties, ownership
  - Mark datasets as `priority_1`, `priority_2`, `priority_3`, or `skip`
  - Expected Phase 1 datasets per theme:
    - **Nursing Homes:** Provider Information, Ownership, Penalties, Health Deficiencies, MDS Quality Measures, Survey Summary
    - **Hospitals:** Hospital General Information, Healthcare Associated Infections, Complications and Deaths, Hospital-Acquired Condition Reduction Program
    - **Home Health:** Home Health Care Agencies, Home Health Care - National Data
    - **Hospice:** Hospice - General Information, Hospice - Provider Data
    - **Doctors/Clinicians:** National Downloadable File, Facility Affiliation Data
    - **Inpatient Rehab:** General Information, Provider Data
    - **Long-Term Care:** General Information, Provider Data

- [x] **Step 3: Analyze Cross-Dataset Join Keys**
  - Map common identifiers across datasets:
    - `CMS Certification Number (CCN)` — primary facility identifier
    - `NPI (National Provider Identifier)` — clinician/org identifier
    - `Federal Provider Number` — used in nursing home datasets
    - `Provider ID` — CMS internal identifier
    - `State`, `County`, `ZIP` — geographic keys
  - Document which identifier each dataset uses as its primary key

- [x] **Step 4: Build AI-Assisted Schema Mapper (Gemini)**
  - Python script that takes a CMS CSV header row + data dictionary PDF and sends to Gemini API
  - Gemini returns: column descriptions, recommended DB column names, data types, nullable flags, and mapping notes
  - Output saved to `data/inspection_notes/cms/{theme}_{dataset_id}_schema_map.md`
  - *Note: Build the task definition and spec now; implementation deferred to execution phase*

- [x] **Step 5: Create User-Selectable Dataset Configuration**
  - Design a YAML/JSON config file (`data/ingestion_config.yaml`) where user can toggle datasets:
    ```yaml
    cms_datasets:
      nursing_homes:
        provider_information:
          enabled: true
          priority: 1
          sync_schedule: "nightly"
        ownership:
          enabled: true
          priority: 1
          sync_schedule: "weekly"
        fire_safety_deficiencies:
          enabled: false
          priority: 3
    ```
  - Ingestion jobs read this config to determine what to sync

---

## Dependencies
- Completion of `ST-04A_data_inspection_setup.md` (directories and sample data available)

## Complexity Rating
**High**

## Definition of Done
- Complete dataset catalog covering all 130+ CMS datasets with metadata, priority tiers, and field mapping notes
- Cross-dataset join key documentation is complete
- User-selectable ingestion config YAML schema is defined
- AI-assisted schema mapper task spec is documented (implementation follows in execution phase)
