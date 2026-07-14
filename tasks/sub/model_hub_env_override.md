# Subtask: Model Registry Environment Override Pattern

**Parent Link:** `base/model_hub.md`

## Actionable Steps
- [x] Support environment overrides for quick model switching without touching the database: `OCR_MODEL`, `ASR_MODEL`, `EMBEDDING_MODEL`, `CLASSIFIER_MODEL`, `COMPLETION_MODEL`.
- [x] Environment overrides take priority over database registry entries.

## Dependencies
- Pydantic Settings composition (`common/config/settings.py`).

## Definition of Done
- Setting any model override environment variable changes the active model without modifying the database entries.
