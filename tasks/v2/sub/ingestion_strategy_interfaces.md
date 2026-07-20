# Subtask: Ingestion Strategy Interfaces

**Parent Link:** `base/03_ingestion_pipeline_strats.md`

## Actionable Steps
- [ ] Create base classes using Python's `abc` module in `projects/syntraflow/src/ingestion/strategies.py`:
  - [ ] `BaseChunker`: Abstract method `chunk(self, text: str) -> list[dict]`.
  - [ ] `BasePreProcessor`: Abstract method `process(self, data: bytes) -> bytes`.
  - [ ] `BasePostProcessor`: Abstract method `enrich(self, chunk: dict) -> dict`.
- [ ] Add type hint definitions and Pydantic input models for custom parameter validation for each strategy type.

## Dependencies
- None

## Definition of Done
- Base classes defined, imported, and unit-tested in `projects/syntraflow/`.
- Concrete implementations fail to initialize unless they implement all required methods.
