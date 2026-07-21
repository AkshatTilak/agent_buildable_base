# Subtask: Evaluation Frontend Attribution

**Parent Link:** `base/05_eval_test_generation.md`

## Actionable Steps
- [x] Build the "Evaluations" panel component: `frontend/src/components/EvalPanel.tsx`.
- [x] Include an evaluation history list showing previous run scores (Faithfulness, Relevance) with status indicators (completed, failed, running).
- [x] Implement UI buttons to:
  - [x] Trigger synthetic test generation.
  - [x] Trigger an asynchronous evaluation run execution.
- [x] Add editable tables allowing operators to manually create, edit, or delete test queries, expected outputs, and contexts.
- [x] Render score progression graphs using charts (e.g. `recharts` or standard canvas).

## Dependencies
- `sub/frontend_api_integration.md`
- `sub/eval_runner_integration.md`

## Definition of Done
- Panel loaded inside the Frontend.
- Users can view and manage test cases and view evaluation charts dynamically.
