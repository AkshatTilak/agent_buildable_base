# Subtask: Ingestion Chunking Implementations

**Parent Link:** `base/03_ingestion_pipeline_strats.md`

## Actionable Steps
- [x] Implement `FixedSizeChunking` inheriting from `BaseChunker`, specifying window sizes and overlaps.
- [x] Implement `RecursiveCharacterChunking` inheriting from `BaseChunker`, splitting progressively on paragraph/sentence bounds.
- [x] Implement `SemanticChunking` inheriting from `BaseChunker`, utilizing Hugging Face/LiteLLM embedding similarity thresholds to slice documents on topic transitions.
- [x] Integrate error-recovery logic: fallback to Recursive if Semantic chunking fails due to local device embedding capacity or remote timeout.

## Dependencies
- `sub/ingestion_strategy_interfaces.md`

## Definition of Done
- All three chunkers are fully implemented and conform to the `BaseChunker` interface.
- Unit tests verify the chunking outputs and correct boundaries on sample text.
