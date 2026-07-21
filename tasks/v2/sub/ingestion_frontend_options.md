# Subtask: Ingestion Frontend Options

**Parent Link:** `base/03_ingestion_pipeline_strats.md`

## Actionable Steps
- [x] Refactor `IngestionPanel.tsx` component.
- [x] Add an "Advanced Ingestion Settings" dropdown accordion.
- [x] Implement select dropdowns for chunking strategies (Fixed, Recursive, Semantic).
- [x] Add slider UI controls for numeric variables like `chunkSize` and `overlap`.
- [x] Add checklist toggles for Pre-Processors (OCR cleanups) and Post-Processors (Metadata extractor, Summarizer).
- [x] Attach selected settings as part of the FormData payload when initiating document upload calls.

## Dependencies
- `sub/frontend_api_integration.md`
- `sub/ingestion_api_endpoints.md`

## Definition of Done
- User interface allows customized upload configurations.
- Submissions trigger successful backend ingest pipelines using the user-specified configuration.
