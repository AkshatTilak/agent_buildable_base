# Subtask: EvalPanel Premium UI Overhaul

**Parent Link:** `base/03_frontend_premium_ui.md`

## Actionable Steps
- [ ] Add eval run history list below charts — show past runs with timestamps, score summaries, and status.
- [ ] Add per-test-case result badges in the table: pass (emerald), fail (rose), skip (amber).
- [ ] Add multi-metric chart support: faithfulness + relevance + context precision + answer correctness.
- [ ] Add "Export CSV" button to download test cases and results.
- [ ] Add bulk import button for test cases from JSON/CSV file.
- [ ] Add expandable test case rows — click to see full query, expected output, context, and actual result.
- [ ] Add toast notifications for eval operations (generate, run, import).
- [ ] Add agent selector dropdown — choose which agent to evaluate (currently hardcoded to "default_agent").

## Dependencies
- `sub/design_shared_components.md` (for Toast, StatusBadge).

## Definition of Done
- Eval run history is visible with timestamps and scores.
- Test case table shows pass/fail badges.
- CSV export/import works.
- Agent selector allows choosing target agent.
