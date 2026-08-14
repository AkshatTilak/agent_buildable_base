# Subtask 04_04: Datastore Bindings Real Integration Tests

## Parent Base Task
`tasks/v8/base/04_hub_management_linking_real.md`

## Objective
Create `tests/integration/gateway/test_datastore_bindings_real.py` — datastore binding & credential encryption.

## Tasks
1. [x] Bind Qdrant collection to hub → verify `DatastoreBinding` row.
2. [x] Bind external DB connector → test credential encryption/decryption.
3. [x] Unbind → verify cleanup.

## Definition of Done
- Datastore binding, credential encryption/decryption, and unbind operations verified against actual running Postgres (`:5432`) and Qdrant (`:6333`).
- If errors arise, inspect Docker container logs (`docker compose logs db qdrant`) and fix root causes directly in backend binding services.
- Deleting test data is not necessary.
