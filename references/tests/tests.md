---
version: 1.0.0
updated: 2026-08-18
id: tests
links:
  - ../../tasks/tasks.md
  - ../../workflows/execution/work_verification.md
  - ../logs/
---

# Tests — Two-Track Testing Strategy

> **Purpose**: Defines the two-track testing strategy and where tests live. Every
> feature and workflow must be covered by both tracks, and edge cases must be
> considered explicitly.

## 1. The Two Tracks

### Track 1 — Unit Tests
- Test individual functions, classes, and modules in isolation.
- Fast, deterministic, no external services.
- Live alongside the code they test (e.g. `tests/unit/`).

### Track 2 — Real System Interaction / Feature & Workflow Tests
- Test actual system interactions: API calls, database round-trips, message
  brokers, Docker containers, end-to-end feature and workflow paths.
- Verify that features and workflows behave correctly as a whole.
- Live in `tests/integration/` and `tests/e2e/` (or equivalent).

> Both tracks are **mandatory**. A feature is not "done" until both tracks pass.

## 2. Edge-Case Coverage Mandate

- Every feature/task must explicitly list its edge cases (see
  `../../tasks/_templates/sub.md`).
- Edge cases include: empty/zero input, boundary values, invalid input, missing
  dependencies, concurrent access, and failure paths.
- Each listed edge case must have a corresponding test.

## 3. Test Logs

- Test output and failures are logged per `../logs/`.
- Test logs must be easy to find and maintain (see `../logs/`).

## 4. Verification

- `../../workflows/execution/work_verification.md` audits that both tracks exist and pass
  before a task is marked `[x]`.
