# Subtask 15_02: MCP Lifecycle Real Integration Tests

## Parent Base Task
`tasks/v8/base/15_mcp_tools_ecosystem.md`

## Objective
Create `tests/integration/gateway/test_mcp_lifecycle_real.py` — full MCP lifecycle using sample tools.

## Tasks
1. [ ] Start `sample_calculator` server as subprocess fixture.
2. [ ] Register via `POST /api/mcp/servers` → verify DB row.
3. [ ] Health check → verify `healthy` status.
4. [ ] Discover tools → verify calculator tools appear in cache.
5. [ ] Invoke `add` tool with `{a: 2, b: 3}` → verify result `5`.
6. [ ] Toggle tool disabled → verify excluded from `GET /api/mcp/tools`.
7. [ ] Update server URL → re-sync → verify tools still work.
8. [ ] Delete server → verify cascading cleanup of tool cache.
9. [ ] Test internal server deletion block (`is_internal = True`).

## Definition of Done
- Full MCP lifecycle test passes against actual running Gateway (`:8000`) and Postgres (`:5432`) (register → health → discover → invoke → toggle → update → delete endpoint test → internal block).
- If errors arise, inspect Docker container logs (`docker compose logs db`) and fix root causes directly.
- Deleting test data is not necessary.
