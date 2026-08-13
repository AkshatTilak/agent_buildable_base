# Subtask 09_01: Extract API Types Script

## Parent Base Task
`tasks/v8/base/09_frontend_backend_contract_tests.md`

## Objective
Create `scripts/extract_api_types.py` — parse `frontend/src/types/api.ts` TypeScript interfaces into JSON Schema for Python-side validation.

## Tasks
1. [ ] Parse `frontend/src/types/api.ts` TypeScript interfaces.
2. [ ] Convert each interface into JSON Schema (field names, types, optional vs required, enums).
3. [ ] Output a machine-readable schema file consumable by Python contract tests.
4. [ ] Handle nested types, unions, and optional fields correctly.

## Definition of Done
- `scripts/extract_api_types.py` parses `types/api.ts` into JSON Schema.
- Output schema is consumable by Python contract tests.
