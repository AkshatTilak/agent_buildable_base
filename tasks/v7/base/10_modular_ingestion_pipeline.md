# Base Task 10: Modular Configurable Ingestion Pipeline & UX (v7)

## Objective
Expand SyntraFlow and Gateway APIs to provide a fully configurable, document-type-aware, multi-step ingestion pipeline. Users can choose specific models and options for each phase (OCR, Chunking, Embeddings, LLM Summarization, Knowledge Graph Extraction) with support for multi-selectable post-processors in both backend APIs and the React frontend UI.

## Subtasks
1. `[x]` `sub_10_01_backend_modular_ingestion_pipeline.md`: Document-type-aware backend pipeline engine & API schemas supporting per-job and per-collection custom configuration (OCR, Chunking, Embedding, LLM Summarizer, KG Entity Extractor, multi-selectable post-processors).
2. `[x]` `sub_10_02_frontend_ingestion_configuration_ux.md`: Advanced document-type-aware ingestion modal & profile settings in React UI with dynamic options and multi-selectable post-processing checkboxes.
