# Base Task 9: Frontend-Backend Contract Tests (v8)

## Objective
Validate that the frontend `api.ts` client functions match the actual gateway API response schemas — catch drift between frontend types and backend models.

## Why
There are no frontend tests and no API-contract validation. Frontend TypeScript types in `types/api.ts` can silently drift from backend Pydantic schemas, causing runtime errors that only surface in the browser.

## Scope
- **`tests/e2e/contracts/test_api_contracts.py`** — For every major API surface in `frontend/src/services/api.ts`:
  - Hit the real gateway endpoint.
  - Validate response JSON against the TypeScript type definitions (parse `types/api.ts` into expected schemas).
  - Confirm status codes, error shapes, pagination structures.
  - Cover: health, auth, hubs, agents, workflows, ingestion, eval, MCP, models, credentials, settings.
- **Frontend type extraction script** — `scripts/extract_api_types.py` — Parses `frontend/src/types/api.ts` TypeScript interfaces into JSON Schema for Python-side validation.
- **Identify type mismatches** between frontend expectations and actual backend responses (field names, types, optional vs required, enum values).

## Associated Subtasks
1. `[ ]` `sub_09_01_extract_api_types.md`: `scripts/extract_api_types.py` — TS interface → JSON Schema extraction.
2. `[ ]` `sub_09_02_api_contract_tests.md`: `tests/e2e/contracts/test_api_contracts.py` — contract validation across all API surfaces.
3. `[ ]` `sub_09_03_fix_type_mismatches.md`: Identify and fix frontend/backend type mismatches found by contract tests.

## Definition of Done
- `scripts/extract_api_types.py` parses `types/api.ts` into JSON Schema.
- Contract tests validate every major API surface against the actual running gateway (`:8000`).
- All identified type mismatches (field names, types, optional/required, enums) are fixed across backend Pydantic models or frontend TypeScript interfaces.
- If errors arise, inspect Docker container logs (`docker compose logs db`) and resolve root causes.
- Deleting test data is not necessary.
