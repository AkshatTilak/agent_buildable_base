# TrueCare Version 1 (v1) — Master Goal & North Star

## System Objective
Build and deploy **TrueCare Version 1**, a unified, multi-surface elder care discovery, facility management, and administration platform powered by a Python-first backend, multi-tier data ingestion pipeline, 3-way hybrid search engine (Spatial PostGIS + Lexical BM25 + Semantic Vector embeddings), and autonomous AI agent subsystems with human-in-the-loop review queues.

---

## Scope & Target Deliverables
1. **Foundation & Scaffolding**: Python `uv` workspace monorepo, PostgreSQL + PostGIS schema, shared libraries (`py-common`, `api-contracts`, `ts-common`), RBAC authentication service, and unified taxonomy core.
2. **Multi-Tier Ingestion Engine** (Expanded):
   - **CMS Provider Data API** sync across 7 provider themes: Nursing Homes (18 datasets), Hospitals (73), Home Health (11), Hospice (8), Doctors/Clinicians (8), Inpatient Rehab (4), Long-Term Care (3).
   - User-selectable dataset configuration (Phase 1: general info, locations, reviews/penalties, ownership).
   - **Vintage Magazine OCR** pipeline using BaiduOCR/PaddleOCR with dual output: evaluation checklists → category seeds, provider tables → `staged_records`.
   - **INCOG Ombudsman PDF** tabular extraction via `pdfplumber`/`camelot`.
   - Fully normalized database schema with source provenance tracking on every record.
   - Staging Review queue, Prefect ETL orchestration DAGs, and human data inspection workflow.
3. **Hybrid Search & AI Subsystem**:
   - `search-service`: Spatial queries, BM25 text search, `pgvector` dense embeddings, and Reciprocal Rank Fusion (RRF).
   - `ai-agent-service`: Facility profile auto-enricher agent, dynamic facility comparison/summarizer agent, and human-in-the-loop verification queue.
   - `leads-service` & `billing-service`: Stark/AKS compliant lead routing, CRM kanban, and tour booking.
4. **Client Applications & Surfaces**:
   - `web-family`: Next.js B2C public search & comparison site (SEO optimized).
   - `web-facility`: Next.js B2B SaaS dashboard for facility admins & admissions.
   - `web-admin`: Next.js internal ERP for verification queues, compliance controls, & audit logs.
   - `mobile`: React Native / Flutter shared mobile shell.

---

## Architectural Base Tasks Checklist

- [x] [BT-01: Foundation, Workspace & Core Services Base](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/base/BT-01_foundation.md)
  - Complexity: High
  - Subtasks: `ST-01`, `ST-02`, `ST-03`
- [ ] [BT-02: Multi-Tier Data Ingestion Engine & Staging Workflows (Expanded)](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/base/BT-02_ingestion_pipeline.md)
  - Complexity: **Very High**
  - Subtasks: `ST-04A`, `ST-04B`, `ST-04C`, `ST-04D`, `ST-05A`, `ST-05B`, `ST-06`, `ST-06B`
- [ ] [BT-03: Search Engine & AI Agent Subsystem](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/base/BT-03_search_ai_engine.md)
  - Complexity: High
  - Subtasks: `ST-07`, `ST-08`, `ST-09`, `ST-10`
- [ ] [BT-04: Client Applications & Shared Mobile Shell](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/base/BT-04_client_surfaces.md)
  - Complexity: High
  - Subtasks: `ST-11`, `ST-12`, `ST-13`, `ST-14`

---

## Version 1 Completion Criteria
Version 1 is marked `[x]` complete only when:
- 100% of underlying Base Tasks (`BT-01` through `BT-04`) and all linked Subtasks are marked `[x]`.
  - BT-01: `ST-01`, `ST-02`, `ST-03` (3 subtasks)
  - BT-02: `ST-04A`, `ST-04B`, `ST-04C`, `ST-04D`, `ST-05A`, `ST-05B`, `ST-06`, `ST-06B` (8 subtasks)
  - BT-03: `ST-07`, `ST-08`, `ST-09`, `ST-10` (4 subtasks)
  - BT-04: `ST-11`, `ST-12`, `ST-13`, `ST-14` (4 subtasks)
  - **Total: 19 subtasks**
- All client applications compile cleanly, communicate with domain services, pass verification tests, and satisfy security and compliance standards.
