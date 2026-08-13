# Subtask 09_02: API Contract Tests

## Parent Base Task
`tasks/v8/base/09_frontend_backend_contract_tests.md`

## Objective
Create `tests/e2e/contracts/test_api_contracts.py` — contract validation across all API surfaces.

## Tasks
1. [ ] For every major API surface in `frontend/src/services/api.ts`:
   - Hit the real gateway endpoint.
   - Validate response JSON against the TypeScript type definitions (parse `types/api.ts` into expected schemas).
   - Confirm status codes, error shapes, pagination structures.
2. [ ] Cover: health, auth, hubs, agents, workflows, ingestion, eval, MCP, models, credentials, settings.

## Definition of Done
- Contract tests validate every major API surface against the real gateway.
- Status codes, error shapes, and pagination structures confirmed.
