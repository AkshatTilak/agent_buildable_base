# Reference: Ingestion & Spatial PDF Extraction Rules (`ingestion_extraction_rules.md`)

**Parent Context:** [references.md](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/references/references.md)
**Related Subtasks:** [ST-05A: Vintage Magazine Extraction](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/sub/ST-05A_vintage_magazine_ocr_pipeline.md), [ST-05B: Ombudsman Tabular Extraction](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/sub/ST-05B_ombudsman_pdf_extraction.md)

---

## 1. INCOG Ombudsman PDF Spatial Extraction Engine (`ST-05B`)

### Layout Anchors & Vertical Coordinates
- **Header Noise Filter**: Skips introductory pages (Pages 1–4) which contain staff names and regional oversight text without table grid lines.
- **Row Bounding**: Anchors each facility block vertically between `OMBUDSMAN [Region Code]` line markers starting on Page 5.

### Horizontal Word Coordinate Bounding Boxes (`x0` Thresholds)
1. **Column 1 (`x0 < 170`)**: Name, License ID, Street Address, City/State/Zip, Administrator Name.
2. **Column 2 (`170 <= x0 < 250`)**: Phone Number.
3. **Column 3 (`250 <= x0 < 360`)**: Medicare Certification (`Yes`/`No`) & Medicare Bed Count.
4. **Column 4 (`360 <= x0 < 440`)**: Medicaid Certification (`Yes`/`No`).
5. **Column 5 (`440 <= x0 < 520`)**: Licensed Bed Capacity (`INTEGER`).
6. **Column 6 (`x0 >= 520`)**: Special Services & Features (parsed as clean bullet arrays).

---

## 2. Vintage Care Guide TOC-Driven Precision Extraction Engine (`ST-05A`)

### Dynamic TOC Section Discovery
- **Discovery Mechanism**: Parses Table of Contents on Pages 5 & 7 (`CONTENTS`) to dynamically extract section page spans for all 42 directory headings across Section 2 (Senior Housing), Section 3 (Special Services), Section 4 (Community Services).
- **Checklist Discovery**: Discovers 7 evaluation checklists via TOC (Pages 23, 28, 30, 32, 34, 36, 38) and extracts community target types, form fields, categories, and checkbox items. Strips reversed vertical margin noise (`snoitadommoccA`, `snalP`, `roolF`, `ytiruceS/ytefaS`, `htlaeH`, `secivreS`, `gnisneciL`).

### Glossary 3-Column Spatial Resolution
- **Discovery**: TOC heading `11 Glossary of Terms`.
- **Spatial 3-Column Split**:
  - Column 1: `x0 < 190`
  - Column 2: `190 <= x0 < 370`
  - Column 3: `x0 >= 370`
- **Boundary Resolution**: Regex positional title matching isolates definition text cleanly across all 20 glossary terms without cross-column merging.

### Directory Grid Tables & Key-Legend Indicator Decoding
- **Multi-Line Facility Name Wrapping & Mid-Line Street Number Split**:
  - Facility names wrapping across 2 lines before street address (e.g. `The Villages at` + `Southern Hills 5721 S. Lewis Ave.`) are resolved by extracting pre-street-number text into `name_parts` and post-street-number text into `address_parts` (reconstructing `"The Villages at Southern Hills"`).
- **Short Name Threshold**: Lowered validation check to `len(name) >= 2` to capture short facility names like `36N`.
- **Structured `features` Dictionary**: Decodes tabular key-legend symbols (`●`, `▲`, `■`, `AI`, `CF`, `HCV`, `LS`, `PP`, `PI`, `WH`, `WP`) into structured feature key-value maps (`"Included"`, `"Available"`, `"Extra Cost"`, `"Community Facilities & W/D Provided"`, `"Partially Included"`, `"W/D Hookups in Unit"`, `"W/D Provided in Unit"`).
- **Partial Levels of Care**: Evaluates column positions against headers (`Independent Living`, `Assisted Living`, `Nursing Care`) to extract exact subsets (e.g. `["Assisted Living", "Nursing Care"]` for *The Villages at Southern Hills*).
- **Promotional Display Ad Filtering**: Filters out full-page display ads and banner callouts containing `"remarkable senior living"`, `"schedule a meeting"`, `"ready to move"`, `"find the best senior"`.

---

## 3. Storage & Lineage Rules
- **JSON Exclusivity**: CSV files removed per pipeline directive; inspectable outputs saved strictly to `.json` in `data/raw_sources/`.
- **PostgreSQL Staging**: Records staged in `staged_records` table with `0.95` confidence score, source PDF metadata, and raw JSON payloads for full auditability.
