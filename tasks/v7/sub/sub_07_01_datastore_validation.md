# Subtask 07_01: Syntraflow Datastore Binding Validation

## Objective
Enforce vector DB connectivity and datastore binding checks in Syntraflow prior to vector collection instantiation to prevent orphaned collections and unhandled database errors.

## Tasks
1. `[x]` Implement datastore connectivity check helper `validate_datastore_binding(datastore_id)` in `projects/syntraflow/services/datastore_validator.py`.
2. `[x]` Update vector collection creation APIs in `projects/syntraflow/api/collections.py` to invoke datastore validation before collection initialization.
3. `[x]` Return structured `400 Bad Request` or `503 Service Unavailable` JSON response with clear error context if target vector DB (Qdrant/Milvus) is unreachable or unbound.
4. `[x]` Add unit tests verifying datastore binding failure modes in `tests/test_syntraflow_validation.py`.
