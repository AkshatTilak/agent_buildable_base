# Subtask: Ingestion Processing Implementations

**Parent Link:** `base/03_ingestion_pipeline_strats.md`

## Actionable Steps
- [x] Implement PreProcessors:
  - [x] `OCRNoiseReduction`: Strip visual artifacts or redundant whitespace from raw OCR inputs.
  - [x] `LanguageFilter`: Detect and normalize character sets.
- [x] Implement PostProcessors:
  - [x] `MetadataExtractor`: Uses Model Registry completion models to extract entities (e.g. author, date, organizations) from chunk content.
  - [x] `SummaryTagger`: Automatically generates short tags/summaries for each chunk and attaches them to `metadata_json`.

## Dependencies
- `sub/ingestion_strategy_interfaces.md`

## Definition of Done
- Pre/Post-processors fully implemented and return valid transformed objects.
- Integration tests confirm metadata extraction works when run against a local mock LLM client.
