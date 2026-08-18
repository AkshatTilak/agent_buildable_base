---
version: 1.0.0
updated: 2026-08-18
id: coding_philosophy
links:
  - CONVENTIONS.md
  - workflows/execution/work_principle.md
  - workflows/execution/work_verification.md
---

# CODING_PHILOSOPHY — How We Write Code

> **Purpose**: The stack-agnostic principles that guide every line of code
> written in this workspace. These are the "why" behind the "how" in
> `workflows/execution/work_principle.md`.

## 1. Explicit Over Implicit

- Behavior must be visible and intentional, not hidden in magic.
- Prefer clear, named constructs over clever one-liners.
- Configuration is explicit and documented, never silently inferred.

## 2. DRY, But Not At The Cost Of Clarity

- Reuse shared logic; refactor duplicates into a single canonical home.
- Do not over-abstract: a shared helper must earn its existence.
- When a duplicate is found, fix the **root source**, not the symptom.

## 3. Type Safety

- Use the project's chosen type checker (see `STACK.md`).
- Model data with explicit, validated types (e.g. Pydantic, dataclasses, TS
  interfaces) so invalid states are caught early.
- The schema is the contract — keep it in sync with the database (see
  `references/db/`).

## 4. Observability First

- Logging, telemetry, and tracing are native to every feature, not bolted on.
- Every failure is logged and traceable; nothing fails silently.
- Logs are structured, findable, and maintained (see `references/logs/`).

## 5. No Blanket Error Handling

- **No** blanket `try/except` that swallows errors.
- Handle errors where they occur, with intent.
- **No hard pre-created fallbacks.** Fallbacks exist only when the user requests
  them (see `workflows/quality/fallback_policy.md`).

## 6. Tests Are First-Class

- Tests are written **concurrently** with features, not after.
- Two tracks: **unit tests** and **real system interaction / feature & workflow
  tests** (see `references/tests/`).
- Edge cases are considered and covered, not assumed away.

## 7. Small, Reviewable Units

- Prefer small, focused changes that are easy to review and verify.
- Each unit has a clear Definition of Done.

## 8. Environment-Aware

- Code and commands respect the recorded OS and shell (`STACK.md`).
- Never assume a platform; generate for the real environment.

## 9. Backward Compatibility Is A Choice

- Prefer backward-compatible changes, but **ask the user** whether to preserve
  backward compatibility or break it (see `workflows/user/user_input.md` and
  `workflows/planning/planning.md`). It is never assumed silently.

## 10. The Philosophy Evolves

- When the same mistake recurs, the philosophy and principles are updated (see
  `workflows/execution/work_verification.md` feedback loop and
  `workflows/quality/recheck_codebase.md`).
