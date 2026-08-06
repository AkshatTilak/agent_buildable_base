# Subtask: ST-05A — Vintage Magazine Precision Extraction Pipeline

**Parent Link:** [BT-02: Multi-Tier Data Ingestion Engine & Staging Workflows](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/base/BT-02_ingestion_pipeline.md)
**Reference Rules:** [ingestion_extraction_rules.md](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/references/logic/ingestion_extraction_rules.md)

## Objective
Build a TOC-driven precision extraction pipeline for vintage local care guide PDFs (`LIFE's Vintage Guide to Housing and Services 2026-2027.pdf`). The pipeline produces three inspectable JSON outputs and stages facility records into PostgreSQL `staged_records`:
1. **Evaluation Checklists** → 7 checklists saved to `checklists/`
2. **Glossary Definitions** → 20 terms & definitions saved to `glossary/`
3. **Provider Directory Listings** → 488 facility records saved to `listings/` and staged into `staged_records`

---

## Technical Actionable Steps

- [x] **Step 1: Set Up Extraction Environment & pdfplumber Abstraction Layer**
  - Configured `pdfplumber` layout parsing and word-coordinate spatial extraction.
  - Implemented `PrecisionVintageExtractor` class in `packages/py-common/src/py_common/extraction/precision_vintage_extractor.py`.

- [x] **Step 2: Build TOC Discovery & Section Page Span Classifier**
  - Dynamically parses Table of Contents on Pages 5 & 7 (`CONTENTS`) to discover page spans for all 42 directory sections (Section 2 Senior Housing, Section 3 Special Services, Section 4 Community Services).
  - Automatically skips non-directory pages and promotional display ad inserts across page spans.

- [x] **Step 3: Build Evaluation Checklist Extractor**
  - Discovers 7 evaluation checklists via TOC (Pages 23, 28, 30, 32, 34, 36, 38).
  - Extracts target community types, form fields, evaluation categories, and checkbox items.
  - Strips reversed vertical margin noise (`snoitadommoccA`, `snalP`, `roolF`, `ytiruceS/ytefaS`, `htlaeH`, `secivreS`, `gnisneciL`).
  - Saved to `data/raw_sources/vintage_pdfs/extracted/checklists/LIFE's Vintage Guide to Housing and Services 2026-2027.pdf_checklists.json`.

- [x] **Step 4: Build Glossary Extractor**
  - Discovers Glossary page via TOC (`11 Glossary of Terms`).
  - Applies 3-column spatial bounds (`x0 < 190`, `190 <= x0 < 370`, `x0 >= 370`) and title boundary regex matching.
  - Extracts all 20 glossary terms & definitions cleanly without cross-column text merging.
  - Saved to `data/raw_sources/vintage_pdfs/extracted/glossary/LIFE's Vintage Guide to Housing and Services 2026-2027.pdf_glossary.json`.

- [x] **Step 5: Build Provider Listing Table Extractor & Symbol Decoder**
  - **Multi-Line Name Wrapping & Mid-Line Street Number Split**: Resolved long facility title cutoffs (e.g. `The Villages at` + `Southern Hills 5721 S. Lewis Ave.` → `"The Villages at Southern Hills"`).
  - **Short Name Threshold**: Lowered validation check to `len(name) >= 2` to capture short facility names like `36N`.
  - **Structured `features` Dictionary**: Decodes key-legend symbols (`●`, `▲`, `■`, `AI`, `CF`, `HCV`, `LS`, `PP`, `PI`, `WH`, `WP`) into structured feature key-value maps (`"Included"`, `"Available"`, `"Extra Cost"`, `"Community Facilities & W/D Provided"`).
  - **Partial Levels of Care**: Evaluates column positions against headers to extract exact subsets (e.g. `["Assisted Living", "Nursing Care"]` for *The Villages at Southern Hills*).
  - **Promotional Display Ad Filtering**: Filters out full-page promotional display ads and banner callouts.
  - Saved to `data/raw_sources/vintage_pdfs/extracted/listings/LIFE's Vintage Guide to Housing and Services 2026-2027.pdf_listings.json` and staged into PostgreSQL `staged_records` (`0.95` confidence score).

- [x] **Step 6: Output Standard & CSV Cleanup**
  - CSV files removed per pipeline directive; inspectable outputs maintained strictly as JSON.

---

## Dependencies
- Completion of `ST-04C_normalized_schema_design.md` (target staging schema)
- Libraries: `pdfplumber`, `sqlalchemy`, `pytest`

## Complexity Rating
**Very High**

## Definition of Done
- 7 evaluation checklists extracted into structured JSON
- 20 glossary terms & definitions extracted cleanly via 3-column spatial parsing
- 488 directory listings extracted with multi-line names, short names (`36N`), partial care levels, and contextual feature dictionaries
- Records staged into PostgreSQL `staged_records` table with high confidence scores (`0.95`)
- All 17 workspace pytest unit tests passing
