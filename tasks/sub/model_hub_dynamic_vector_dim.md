# Subtask: Model Registry Dynamic Vector Dimension

**Parent Link:** `base/model_hub.md`

## Actionable Steps
- [x] Qdrant collection creation reads `vector_dim` from the active embedding model's registry entry.
- [x] Detect dimension mismatches on startup and warn or block if the active model dimension does not match Qdrant schema.

## Dependencies
- Qdrant client (`qdrant.py`), Model Registry.

## Definition of Done
- Vector collection schema dimension is dynamically determined by the active embedding model registry entry.
