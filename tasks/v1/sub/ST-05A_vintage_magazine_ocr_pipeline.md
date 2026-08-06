# Subtask: ST-05A — Vintage Magazine OCR & Extraction Pipeline

**Parent Link:** [BT-02: Multi-Tier Data Ingestion Engine & Staging Workflows](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/base/BT-02_ingestion_pipeline.md)

## Objective
Build a dual-output OCR extraction pipeline using **BaiduOCR (PaddleOCR)** for processing vintage local care guide PDFs. The pipeline produces two distinct outputs:
1. **Evaluation Checklists** → category/taxonomy seeds (feeding into dataset categorization and filtering)
2. **Provider Listing Tables** → facility records staged into `staged_records`

Additionally, extract the glossary page for domain terminology definitions.

---

## Technical Actionable Steps

- [ ] **Step 1: Set Up BaiduOCR / PaddleOCR Environment**
  - Install PaddleOCR from HuggingFace / PyPI (`paddleocr`, `paddlepaddle`)
  - Configure OCR model for English text recognition with table structure detection
  - Build OCR provider abstraction layer (interface: `extract_text(image) → structured_blocks`) so alternative providers can be swapped in later

- [ ] **Step 2: Build PDF Page Classifier & Table of Contents Parser**
  - Parse the PDF's table of contents to identify page ranges for:
    - **Evaluation Checklists**: Pages matching patterns like "Evaluation Checklist", "Evaluation Guide"
    - **Provider Listing Tables**: Pages under headings like "Independent Living", "Assisted Living", "Nursing Facilities", "Home Health Agencies", "Hospice Care Agencies"
    - **Special Services**: "Adult Day Health", "PACE", "Inpatient Rehabilitation", "Mental/Behavioral Health"
    - **Glossary**: Pages matching "Glossary" heading
  - Classify each page as: `checklist`, `provider_listing`, `glossary`, `bloat` (ads, covers, sponsor pages)
  - Skip `bloat` pages entirely

- [ ] **Step 3: Build Evaluation Checklist Extractor**
  - For pages classified as `checklist`:
    - OCR the page, then parse structured checklist items (question text, category, subcategory)
    - Output structured JSON:
      ```json
      {
        "checklist_type": "Assisted Living Community Evaluation",
        "source_pdf": "iowa_vintage_2024.pdf",
        "source_page": 32,
        "items": [
          {"category": "Staff", "question": "What is the staff-to-resident ratio?", "notes": ""},
          {"category": "Safety", "question": "Is the facility licensed?", "notes": ""}
        ]
      }
      ```
  - Save to `data/raw_sources/vintage_pdfs/extracted/checklists/`
  - These will later inform category creation for our filtered/cleaned dataset

- [ ] **Step 4: Build Provider Listing Table Extractor**
  - For pages classified as `provider_listing`:
    - Use PaddleOCR table structure recognition to detect table boundaries
    - Handle multi-page table continuation (tables spanning 5-20+ pages)
    - Extract column headers and row data, reconciling across page breaks
    - Output structured records with: facility name, address, phone, city, bed count/capacity, license, category (e.g., "Assisted Living", "Nursing Facility")
  - Assign confidence scores per record based on OCR character confidence
  - Write to `staged_records` with `source_type = 'pdf_ocr'`, `extraction_method = 'paddleocr'`

- [ ] **Step 5: Build Glossary Extractor**
  - Extract glossary page terms and definitions
  - Output to `data/raw_sources/vintage_pdfs/extracted/glossary/`
  - Format: `{"term": "PACE", "definition": "Program of All-Inclusive Care for the Elderly..."}`

- [ ] **Step 6: Build Processing Directory Watcher**
  - When user drops a new PDF into `data/raw_sources/vintage_pdfs/`, the pipeline:
    1. Classifies pages via TOC parser
    2. Runs appropriate extractor per page type
    3. Writes outputs to `extracted/` subdirectory
    4. Logs processing results and confidence scores

---

## Dependencies
- Completion of `ST-04C_normalized_schema_design.md` (target staging schema)
- Libraries: `paddleocr`, `paddlepaddle`, `pdf2image`, `pdfplumber` (for TOC text extraction), `Pillow`

## Complexity Rating
**Very High**

## Definition of Done
- PaddleOCR successfully processes sample vintage magazine pages
- Evaluation checklists extracted into structured JSON with question/category taxonomy
- Provider listing tables parsed across multi-page spans into staged facility records
- Glossary terms extracted into structured format
- Bloat pages (ads, sponsors) are correctly identified and skipped
- Confidence scores assigned per extracted record
