# Subtask 04_03: Hub Links Real Integration Tests

## Parent Base Task
`tasks/v8/base/04_hub_management_linking_real.md`

## Objective
Create `tests/integration/gateway/test_hub_links_real.py` — hub linking & cross-hub access.

## Tasks
1. [x] Create link between two hubs → verify bidirectional access.
2. [x] Cross-hub data access (agent hub accessing ingestion hub's collections).
3. [x] Link revocation → verify access denied.
4. [x] Link visibility (which resources are exposed through links).

## Definition of Done
- Hub linking, cross-hub access, link revocation, and link visibility verified against actual running Postgres (`:5432`).
- If errors arise, inspect Docker container logs (`docker compose logs db`) and fix root causes directly in backend hub-linking code.
- Deleting test data is not necessary.
