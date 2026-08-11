# Subtask 10_01: Backend Modular Document-Type-Aware Ingestion Pipeline

## Objective
Extend SyntraFlow ingestion engine, data schemas, ORM models, and Gateway API endpoints to support document-type-dependent, multi-step pipeline choices (OCR Engine, Chunking Strategy & Sliders, Embedding Model, LLM Summary Model, Knowledge Graph Entity Extractor Model, and multi-selectable post-processors).

## Tasks
1. `[x]` Update `IngestDocumentPayload`, `TextIngestPayload`, and `CollectionCreatePayload` schemas in `gateway/api/ingestion_hub.py` to accept document-type-aware execution parameters: `ocr_engine`, `chunking_strategy`, `chunk_size`, `chunk_overlap`, `embedding_model`, `summary_model`, `graph_model`, and `post_processors` (list of multi-selectable steps: `["summary_gen", "keyphrase_extract", "kg_extract", "table_preserve"]`).
2. `[x]` Extend `SyntraFlowJob` and `SyntraFlowCollection` in `projects/syntraflow/src/database/models.py` to store `pipeline_config_json` containing selected pipeline engines and model IDs.
3. `[x]` Update `ingest_document_pipeline` in `projects/syntraflow/src/ingestion/pipeline.py` to dynamically branch based on document type (PDF/Docx vs Audio/Video vs Plain Text vs Images) and execute only the selected OCR/ASR engine, chunker, embedding model, and multi-selected post-processing passes.
4. `[x]` Update `process_ingestion_job` in `projects/syntraflow/src/worker.py` to pass full pipeline configuration dict into pipeline functions and handle stage-by-stage error recovery and Qdrant/Postgres persistence verification.
5. `[x]` Add unit and integration test coverage in `tests/test_syntraflow_validation.py` verifying document-type-dependent pipeline dispatch and multi-selectable post-processor execution.
