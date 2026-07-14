# Subtask: Model Registry Storage

**Parent Link:** `base/model_hub.md`

## Actionable Steps
- [x] Maintain a `model_registry` table in PostgreSQL.
- [x] Seed the table with all supported model entries on first startup.
- [x] Use the `model_registry_` prefix for namespace isolation.

## Dependencies
- PostgreSQL database, SQLAlchemy connection.

## Definition of Done
- `model_registry` table created and seeded with OCR, ASR, Embedding, Classifier, and Completion models.
