# Subtask 16_03: External DB Credentials Vault Real Integration Tests

## Parent Base Task
`tasks/v8/base/16_comprehensive_api_surface_coverage.md`

## Objective
Create `tests/integration/gateway/test_db_credentials_real.py` — external DB vault.

## Tasks
1. [ ] Create Postgres credential → verify encrypted payload in DB.
2. [ ] Test connection → verify success/failure response.
3. [ ] Generate DB MCP tools for credential → verify tool definitions.
4. [ ] Update credential → verify re-encryption.
5. [ ] Delete credential → verify cleanup.

## Definition of Done
- External DB credentials vault verified against actual running Postgres (`:5432`) (create/encrypt, test connection, DB MCP tools, update/re-encrypt, delete endpoint test).
- If errors arise, inspect Docker container logs (`docker compose logs db`) and fix root causes directly.
- Deleting test data is not necessary.
