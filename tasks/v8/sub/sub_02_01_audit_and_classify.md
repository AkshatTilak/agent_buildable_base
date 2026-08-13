# Subtask 02_01: Audit & Classify Existing Tests

## Parent Base Task
`tasks/v8/base/02_reorganize_existing_tests.md`

## Objective
Audit all 77 existing test files in `tests/` and classify each as `unit`, `integration`, or `e2e` based on what it actually tests vs. what it mocks.

## Tasks
1. [ ] Enumerate all test files in `tests/` (77 files).
2. [ ] For each file, determine:
   - What it tests (auth, hubs, ingestion, agents, workflows, eval, MCP, proxy, etc.).
   - What it mocks (SQLite in-memory, MagicMock for Qdrant/Neo4j/Redis/LiteLLM).
   - Classification: `unit` (mock-based, no IO), `integration` (real DB/services), `e2e` (multi-step cross-service).
3. [ ] Produce a classification matrix mapping each file → target directory + marker.
4. [ ] Also audit `projects/*/tests/` submodule tests for consolidation.

## Definition of Done
- Every existing test file classified as unit/integration/e2e.
- Classification matrix produced mapping each file to its target directory and marker.
- Submodule tests identified for consolidation.
