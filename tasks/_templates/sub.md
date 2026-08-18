---
id: sub_001
version: 1.0.0
updated: YYYY-MM-DD
status: not_started
parent: base_001
depends_on: []
links:
  - ../../features/<feature>.md
  - ../../references/structure/<schema>.md
  - supersedes: ../../tasks/sub/<older_task>.md
---

# Subtask: <Actionable Unit>

## Parent Link
`base/<task_name>.md`

## Actionable Steps
- [ ] Step 1: ...
- [ ] Step 2: ...
- [ ] Step 3: ...

## Dependencies
- Required packages / environment variables / other subtasks that must complete first.

## Edge Cases
- [ ] Edge case 1 that must be handled and tested.
- [ ] Edge case 2 that must be handled and tested.

## Definition of Done
- [ ] Explicit criteria required before marking this subtask `[x]`.
- [ ] Unit tests written and passing.
- [ ] Real system interaction / feature & workflow tests written and passing.
- [ ] Logging/tracing added per `../../references/logs/`.
- [ ] `../../workflows/execution/work_verification.md` flow passed.

## Changelog
- `1.0.0` (YYYY-MM-DD): Initial subtask definition.
