# Subtask 01_04: Test Directory Restructure

## Parent Base Task
`tasks/v8/base/01_test_infrastructure_foundation.md`

## Objective
Reorganize the flat `tests/` directory into the layered hierarchy.

## Tasks
1. [x] Create the new directory structure:
   ```
   tests/
   ├── conftest.py                  # Root fixtures & markers
   ├── unit/                        # Fast, mock-based, no-IO tests
   ├── integration/                 # Real DB, real services
   │   ├── gateway/
   │   ├── syntraflow/
   │   ├── guardroute/
   │   └── evalops/
   ├── streaming/                   # WebSocket & SSE streaming tests
   ├── e2e/                         # Multi-step, cross-service workflow tests
   │   ├── flows/
   │   └── contracts/
   ├── live_api/                    # Tests requiring external API keys
   └── performance/                 # Response-time benchmarks, caching, load tests
   ```
2. [x] Add `__init__.py` files where needed for pytest import resolution.
3. [x] Ensure `testpaths` in pytest config points to the new directories.

## Definition of Done
- The layered `tests/` hierarchy exists with all subdirectories.
- `pytest` discovers tests in the new structure.
