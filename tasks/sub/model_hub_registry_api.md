# Subtask: Model Registry API

**Parent Link:** `base/model_hub.md`

## Actionable Steps
- [x] Implement `get_model_spec(role, mode="auto")` to resolve the active model.
- [x] Implement `get_active_model(role)` to get the current default model.
- [x] Implement `list_available(role)` to list all enabled models.
- [x] Implement `get_fallback_chain(role)` to get an ordered fallback list.

## Dependencies
- PostgreSQL client (`postgres.py`), Pydantic models for registry types.

## Definition of Done
- All four registry interface methods are functional, tested, and return correctly resolved ModelSpec records.
