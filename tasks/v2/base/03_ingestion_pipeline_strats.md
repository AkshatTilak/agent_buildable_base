# Base Task: Dynamic Ingestion & Chunking Strategies

## Objective
Refactor SyntraFlow to support highly customizable ingestion pipelines where users can select chunking strategies, pre-processors, and post-processors dynamically.

## Business/System Value
Documents and videos require vastly different processing depending on the use case. A rigid ingestion pipeline fails on edge cases. By exposing "choosable options" for chunking (Semantic, Recursive) and pre/post-processing (OCR cleanup, entity extraction), operators can tailor the ingestion strategy specifically to the data being uploaded, significantly improving RAG quality.

## Complexity Rating
Medium (Refactoring existing monolithic pipelines into a Strategy Pattern architecture).

## Subtask Registry
* `[ ] sub/ingestion_strategy_interfaces.md` — Define abstract base classes for Chunkers, PreProcessors, and PostProcessors in Python.
* `[ ] sub/ingestion_chunking_impl.md` — Implement `RecursiveCharacterChunking`, `SemanticChunking`, and `FixedSizeChunking` classes.
* `[ ] sub/ingestion_processing_impl.md` — Implement pre-processors (OCR noise reduction) and post-processors (Metadata extractor, Summarizer).
* `[ ] sub/ingestion_api_endpoints.md` — Update Gateway API to accept strategy selections in the upload request.
* `[x] sub/ingestion_frontend_options.md` — Add UI dropdowns in the frontend to select ingestion strategies before upload.
