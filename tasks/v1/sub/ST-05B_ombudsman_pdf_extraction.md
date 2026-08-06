# Subtask: ST-05B — INCOG Ombudsman PDF Tabular Extraction

**Parent Link:** [BT-02: Multi-Tier Data Ingestion Engine & Staging Workflows](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/base/BT-02_ingestion_pipeline.md)
**Reference Rules:** [ingestion_extraction_rules.md](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/references/logic/ingestion_extraction_rules.md)

## Objective
Build a dedicated 6-column spatial extraction pipeline for state ombudsman directory PDFs (`INCOG_IAAA_Ombudsman_Nursing_Home_Directory.pdf`). Using `pdfplumber` word-coordinate spatial bounding boxes, extract facility records, license IDs, Medicare/Medicaid bed capacities, administrator names, and special services arrays into PostgreSQL `staged_records`.

---

## Technical Actionable Steps

- [x] **Step 1: Analyze INCOG PDF Structure & Layout Anchors**
  - Analyzed 6-column Ombudsman PDF table layout.
  - Set header noise filter for Pages 1–4 (cover page and staff directory).
  - Anchored vertical row boundaries between `OMBUDSMAN [Region Code]` line markers starting on Page 5.

- [x] **Step 2: Implement Spatial Bounding-Box PDF Extractor**
  - Built `PrecisionOmbudsmanExtractor` in `packages/py-common/src/py_common/extraction/precision_ombudsman_extractor.py`.
  - Configured word coordinate (`x0`) spatial column bounds:
    - Column 1 (`x0 < 170`): Name, License ID, Street Address, City/State/Zip, Administrator Name.
    - Column 2 (`170 <= x0 < 250`): Phone Number.
    - Column 3 (`250 <= x0 < 360`): Medicare Certification & Medicare Bed Count.
    - Column 4 (`360 <= x0 < 440`): Medicaid Certification.
    - Column 5 (`440 <= x0 < 520`): Licensed Bed Capacity.
    - Column 6 (`x0 >= 520`): Special Services & Features (Bullet Points).
  - Extracted 37 complete, verified facility records per PDF with zero column drift.

- [x] **Step 3: Implement Data Cleaning & JSON Artifact Export**
  - Normalized phone numbers, zip codes, and state abbreviations.
  - Saved output to `data/raw_sources/ombudsman_pdfs/extracted/listings/INCOG_IAAA_Ombudsman_Nursing_Home_Directory.pdf_listings.json`.
  - Removed CSV files per pipeline standard.

- [x] **Step 4: Write Extracted Records to PostgreSQL Staging Queue**
  - Inserted into `staged_records` with:
    - `target_table` = `'providers'`
    - `extraction_method` = `'precision_ombudsman_pdf'`
    - `confidence_score` = `0.95`
    - `review_status` = `'pending_review'`

- [x] **Step 5: Batch Processing Script**
  - Implemented `scripts/process_ombudsman_pdfs.py` to batch-process all Ombudsman PDFs in `data/raw_sources/ombudsman_pdfs/`.

---

## Dependencies
- Completion of `ST-04C_normalized_schema_design.md` (target staging schema)
- Libraries: `pdfplumber`, `sqlalchemy`, `pytest`

## Complexity Rating
**Medium**

## Definition of Done
- INCOG sample PDF fully extracted with all 6 columns mapped (License ID, Bed Counts, Medicare/Medicaid status, Special Services)
- Records written to `staged_records` with source provenance and `0.95` confidence scores
- Inspectable JSON written to `data/raw_sources/ombudsman_pdfs/extracted/listings/`
- All 17 workspace pytest unit tests passing
