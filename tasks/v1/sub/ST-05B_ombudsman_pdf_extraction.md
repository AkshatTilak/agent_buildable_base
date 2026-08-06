# Subtask: ST-05B — INCOG Ombudsman PDF Tabular Extraction

**Parent Link:** [BT-02: Multi-Tier Data Ingestion Engine & Staging Workflows](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/base/BT-02_ingestion_pipeline.md)

## Objective
Build a dedicated extraction pipeline for small, well-structured state ombudsman directory PDFs (e.g., INCOG Iowa Ombudsman Nursing Home Directory). These PDFs contain clean tabular data that can be fully processed without OCR using `pdfplumber` / `camelot`.

---

## Technical Actionable Steps

- [ ] **Step 1: Analyze INCOG PDF Structure**
  - Download sample: `https://www.incog.org/agency_on_aging/Documents/IAAA%20Ombudsman%20%20Nursing%20Home%20Directory.pdf`
  - Identify table structure: headers, column count, row delimiters
  - Document page layout and any header/footer noise to strip

- [ ] **Step 2: Implement Tabular PDF Extractor**
  - Use `pdfplumber` (preferred) or `camelot-py` for table detection and extraction
  - Extract all table rows across all pages
  - Standardize column mapping:
    - Facility Name, Address, City, State, ZIP
    - Phone Number, County
    - License ID / Certification Number
    - Bed Count / Capacity
    - Ombudsman Contact / Region
  - Handle merged cells, multi-line rows, and page-break row splits

- [ ] **Step 3: Implement Data Cleaning & Normalization**
  - Strip whitespace, normalize phone number formats
  - Geocode addresses to PostGIS coordinates (using a geocoding service or Nominatim)
  - Validate ZIP codes and state abbreviations
  - Assign high confidence scores (these are well-structured government documents)

- [ ] **Step 4: Write Extracted Records to Staging Queue**
  - Insert into `staged_records` with:
    - `data_source_id` → registered `data_sources` entry for this specific ombudsman directory
    - `source_type = 'pdf_tabular'`
    - `extraction_method = 'pdfplumber'`
    - `confidence_score` → typically 0.90+ for clean tabular PDFs
    - `review_status = 'pending_review'` (even high-confidence records get human review for state directory data)

- [ ] **Step 5: Build Batch Processing for Multiple State PDFs**
  - Support processing all PDFs in `data/raw_sources/ombudsman_pdfs/` directory
  - Register each PDF as a distinct `data_source` entry
  - Log per-PDF extraction metrics to `ingestion_runs`

---

## Dependencies
- Completion of `ST-04C_normalized_schema_design.md` (target staging schema)
- Libraries: `pdfplumber`, `camelot-py`, `geopy` (for optional geocoding)

## Complexity Rating
**Medium**

## Definition of Done
- INCOG sample PDF fully extracted into structured facility records
- All rows parsed with correct column mapping and clean data
- Records written to `staged_records` with source provenance and confidence scores
- Pipeline handles multiple ombudsman PDFs from the drop directory
- Extraction metrics logged in `ingestion_runs`
