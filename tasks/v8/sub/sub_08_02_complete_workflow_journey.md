# Subtask 08_02: Complete Workflow Journey E2E Test

## Parent Base Task
`tasks/v8/base/08_end_to_end_workflow_tests.md`

## Objective
Create `tests/e2e/flows/test_complete_workflow_journey.py` — full workflow journey.

## Tasks
1. [x] Register user → create workflow hub (unique identifiers).
2. [x] Design a multi-node workflow (LLM node using `gemini/gemma-4-26b-a4b-it` → FinalMessageNode branches).
3. [x] Execute workflow run → verify step-by-step execution log.
4. [x] Save workflow version → modify → save new version → restore old version.
5. [x] Export workflow → import into new hub → verify portability.
6. [x] Verify workflow operations without mandatory post-test data deletion.

## Definition of Done
- Full workflow journey passes end-to-end against actual running dev services.
- Versioning and import/export portability verified.
- If errors arise, inspect Docker container logs (`docker compose logs`) and fix the root causes in the backend, frontend, or submodules.
- Deleting test data is not necessary.
