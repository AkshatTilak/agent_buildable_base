# Subtask: Evaluation Frontend Attribution

**Parent Link:** `base/05_eval_test_generation.md`

## Actionable Steps
- [ ] Build the "Evaluations" panel component: `frontend/src/components/EvalPanel.tsx`.
- [ ] Include an evaluation history list showing previous run scores (Faithfulness, Relevance) with status indicators (completed, failed, running).
- [ ] Implement UI buttons to:
  - [ ] Trigger synthetic test generation.
  - [ ] Trigger an asynchronous evaluation run execution.
- [ ] Add editable tables allowing operators to manually create, edit, or delete test queries, expected outputs, and contexts.
- [ ] Render score progression graphs using charts (e.g. `recharts` or standard canvas).

## Dependencies
- `sub/frontend_api_integration.md`
- `sub/eval_runner_integration.md`

## Definition of Done
- Panel loaded inside the Frontend.
- Users can view and manage test cases and view evaluation charts dynamically.
