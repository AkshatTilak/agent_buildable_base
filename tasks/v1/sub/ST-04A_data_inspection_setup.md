# Subtask: ST-04A — Data Inspection Setup & Directory Structure

**Parent Link:** [BT-02: Multi-Tier Data Ingestion Engine & Staging Workflows](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/base/BT-02_ingestion_pipeline.md)

## Objective
Create the raw data source directory structure, sample CMS CSV downloader, and human inspection notes framework so the user can personally inspect, annotate, and guide schema decisions before pipelines are coded.

---

## Technical Actionable Steps

- [x] **Step 1: Create Raw Source Directory Tree**
  ```
  data/
  ├── raw_sources/
  │   ├── cms/
  │   │   ├── nursing_homes/          # CMS Nursing Homes CSVs
  │   │   ├── hospitals/              # CMS Hospitals CSVs
  │   │   ├── home_health/            # CMS Home Health CSVs
  │   │   ├── hospice/                # CMS Hospice CSVs
  │   │   ├── doctors_clinicians/     # CMS Doctors & Clinicians CSVs
  │   │   ├── inpatient_rehab/        # CMS Inpatient Rehab CSVs
  │   │   └── long_term_care/         # CMS Long-Term Care CSVs
  │   ├── vintage_pdfs/               # User drops vintage magazine PDFs here
  │   └── ombudsman_pdfs/             # User drops INCOG/state directory PDFs here
  ├── inspection_notes/               # Human analysis notes (.md per dataset)
  │   ├── cms/
  │   ├── vintage/
  │   └── ombudsman/
  └── data_dictionaries/              # CMS data dictionary PDFs (auto-downloaded)
  ```

- [x] **Step 2: Build CMS Sample Data Downloader Script**
  - Python script (`scripts/download_cms_samples.py`) that:
    - Hits CMS Provider Data API metastore endpoint to get all dataset metadata
    - Downloads each CSV file (or first 100 rows via API query)
    - Saves to the appropriate `data/raw_sources/cms/{theme}/` directory
    - Downloads matching data dictionary PDFs to `data/data_dictionaries/`
  - Prints a summary table: dataset name, row count, column count, file size

- [x] **Step 3: Create Inspection Notes Template**
  - Template `.md` file for `data/inspection_notes/` with sections:
    - Dataset Name & Source URL
    - Column Inventory (auto-populated from CSV headers)
    - Relevance Assessment (keep / skip / partial)
    - Schema Mapping Notes (which TrueCare table/column this maps to)
    - Data Quality Observations (nulls, encoding issues, format quirks)
    - User Decision & Rationale

- [x] **Step 4: Create `.gitignore` Rules for Raw Data**
  - Ensure `data/raw_sources/` CSVs and PDFs are git-ignored (large binary files)
  - Keep `data/inspection_notes/` tracked in git

---

## Dependencies
- Completion of `ST-01_monorepo_and_db_scaffold.md` (for root project structure)

## Complexity Rating
**Low**

## Definition of Done
- Directory tree exists and is documented
- Sample CMS CSVs are downloadable via script for at least the Phase 1 priority datasets
- User can drop vintage/ombudsman PDFs into designated directories
- Inspection notes template is ready for human annotation
