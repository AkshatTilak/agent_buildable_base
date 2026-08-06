# Base Task: BT-02 — Multi-Tier Data Ingestion Engine & Staging Workflows (Expanded)

## Objective
Build the complete multi-tier data collection, inspection, analysis, and ETL ingestion platform covering:
- **CMS Provider Data API** (130+ datasets across 7 provider themes: Nursing Homes, Hospitals, Home Health, Hospice, Doctors/Clinicians, Inpatient Rehab, Long-Term Care)
- **Vintage Magazine Precision Extraction Pipeline** (`pdfplumber` TOC-driven parser for evaluation checklists, 3-column glossary, and directory grid tables)
- **INCOG Ombudsman PDF Tabular Extraction** (6-column spatial bounding-box state directory parsing)
- **Human-in-the-loop data inspection workflow** with raw source directories and inspection notes
- **Reference Rules**: [ingestion_extraction_rules.md](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/references/logic/ingestion_extraction_rules.md)

All pipelines feed into a normalized PostgreSQL schema with source provenance tracking and a unified staging review queue.

## Business / System Value
- Ensures TrueCare has the most comprehensive elder care facility dataset in the market by covering **all** major CMS provider categories
- Provides families with rich, multi-source quality data (ratings, deficiencies, penalties, ownership chains, staffing ratios)
- Preserves full data provenance and source attribution for compliance and trust
- Supports expansion to additional data sources via a user-selectable dataset configuration system

---

## Subtask Registry

### Phase 0: Inspection & Research
- [x] [ST-04A: Data Inspection Setup & Directory Structure](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/sub/ST-04A_data_inspection_setup.md)
  - Details: Create `data/raw_sources/` directory tree (CMS CSVs, vintage PDFs, ombudsman PDFs), `data/inspection_notes/` for human analysis, and sample CMS CSV downloader script.
- [x] [ST-04B: CMS Dataset Research & Schema Analysis](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/sub/ST-04B_cms_dataset_research.md)
  - Details: Systematic analysis of 130+ CMS datasets, AI-assisted (Gemini) column mapping, dataset priority tiering, and user-selectable dataset configuration system.

### Phase 1: Schema & Core Pipelines
- [x] [ST-04C: Normalized Database Schema Design](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/sub/ST-04C_normalized_schema_design.md)
  - Details: Fully normalized PostgreSQL schema for providers, deficiencies, penalties, ownership chains, staffing ratios, quality measures, and patient surveys. Full source provenance tracking on every record.
- [x] [ST-04D: Tier 1 CMS API Ingestion Pipelines](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/sub/ST-04D_ingestion_tier1_cms_api.md)
  - Details: Per-theme CMS sync jobs across all 7 provider categories. Phase 1 priority: general info, locations/names, reviews/penalties, ownership. User-selectable dataset config.

### Phase 2: PDF Extraction Pipelines
- [x] [ST-05A: Vintage Magazine OCR & Extraction Pipeline](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/sub/ST-05A_vintage_magazine_ocr_pipeline.md)
  - Details: TOC-driven `pdfplumber` pipeline. Triple-output: 7 evaluation checklists (`checklists/`), 20 glossary definitions (`glossary/`), 488 directory listings (`listings/`) with contextual feature dictionaries, multi-line title resolution, and symbol key-legend decoding.
- [x] [ST-05B: INCOG Ombudsman PDF Tabular Extraction](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/sub/ST-05B_ombudsman_pdf_extraction.md)
  - Details: 6-column spatial bounding-box `pdfplumber` extractor (`x0` thresholds: `< 170`, `170-250`, `250-360`, `360-440`, `440-520`, `>= 520`) anchored on `OMBUDSMAN [Region]` blocks. Direct staging with high confidence (`0.95`).

### Phase 3: Orchestration & Staging
- [x] [ST-06: Ingestion Staging Queue & Review APIs](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/sub/ST-06_ingestion_staging_queue.md)
  - Details: `staged_records` queue management, approval/rejection APIs, bulk review, atomic record promotion, and source provenance audit trail.
- [ ] [ST-06B: Prefect DAG Orchestration & Scheduling](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/sub/ST-06B_prefect_dag_orchestration.md)
  - Details: Prefect ETL DAGs for all CMS sync jobs, PDF ingestion triggers, health digest, and `ingestion_runs` telemetry.

---

## Complexity Rating
**Very High** — Covers 130+ CMS API datasets, normalized multi-table schema design, spatial PDF extraction, 3 distinct extraction pipeline architectures, user-selectable dataset configuration, and full source provenance tracking.

## Acceptance Criteria
- `data/raw_sources/` directories exist with sample CMS CSVs downloaded and inspectable
- Normalized schema covers providers, deficiencies, penalties, ownership, staffing, quality measures, surveys — with source provenance on every record
- Phase 1 CMS sync jobs (general info, locations, reviews/penalties, ownership) execute across all 7 provider themes
- User-selectable dataset configuration allows toggling individual CMS datasets on/off
- Vintage magazine extraction produces structured checklist categories, glossary definitions, AND staged facility records with contextual feature dictionaries
- INCOG Ombudsman PDFs extract cleanly into `staged_records` with all 6 columns mapped
- All ingested records track their data source, extraction timestamp, and confidence score
- Multiple review sources per facility are supported (CMS is not the only source)
