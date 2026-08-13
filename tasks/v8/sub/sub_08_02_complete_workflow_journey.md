# Subtask 08_02: Complete Workflow Journey E2E Test

## Parent Base Task
`tasks/v8/base/08_end_to_end_workflow_tests.md`

## Objective
Create `tests/e2e/flows/test_complete_workflow_journey.py` — full workflow journey.

## Tasks
1. [ ] Register user → create workflow hub.
2. [ ] Design a multi-node workflow (LLM node using `gemini/gemini-3.5-flash` → conditional → two branches).
3. [ ] Execute workflow run → verify step-by-step execution log.
4. [ ] Save workflow version → modify → save new version → restore old version.
5. [ ] Export workflow → import into new hub → verify portability.
6. [ ] Cleanup.

## Definition of Done
- Full workflow journey passes end-to-end.
- Versioning and import/export portability verified.
- All resources cleaned up.
