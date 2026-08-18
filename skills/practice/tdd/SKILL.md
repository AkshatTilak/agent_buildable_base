---
version: 2.0.0
updated: 2026-08-18
id: skill_practice_tdd
links:
  - ../../../references/tests/tests.md
  - ../../../workflows/execution/work_verification.md
  - ../../../workflows/quality/fallback_policy.md
  - ../../../CONVENTIONS.md
---

# Skill: Test-Driven Development (TDD)

> **Purpose**: Enforce the red-green-refactor test-first cycle. Every
> production code change is driven by a failing test written at a pre-agreed
> public interface seam. Adapted from obra/superpowers and mattpocock/skills.

## Trigger Conditions
- Load this skill when writing or modifying production code.
- Load when a task's Definition of Done requires tests (see
  `../../../references/tests/tests.md`).
- Load when the user explicitly requests TDD.

## The Iron Law

```
NO PRODUCTION CODE WITHOUT A FAILING TEST FIRST
```

Write code before the test? Delete it. Start over.

**No exceptions:**
- Don't keep it as "reference"
- Don't "adapt" it while writing tests
- Don't look at it
- Delete means delete

Implement fresh from tests. Period.

## When to Use

**Always:**
- New features
- Bug fixes
- Refactoring
- Behavior changes

**Exceptions (ask your human partner):**
- Throwaway prototypes
- Generated code
- Configuration files

Thinking "skip TDD just this once"? Stop. That's rationalization.

## Red-Green-Refactor

### RED — Write Failing Test

Write one minimal test showing what should happen.

**Requirements:**
- One behavior
- Clear name
- Real code (no mocks unless unavoidable)

**Verify RED — Watch It Fail**

**MANDATORY. Never skip.**

Confirm:
- Test fails (not errors)
- Failure message is expected
- Fails because feature missing (not typos)

**Test passes?** You're testing existing behavior. Fix test.

**Test errors?** Fix error, re-run until it fails correctly.

### GREEN — Minimal Code

Write simplest code to pass the test.

Don't add features, refactor other code, or "improve" beyond the test.

**Verify GREEN — Watch It Pass**

**MANDATORY.**

Confirm:
- Test passes
- Other tests still pass
- Output pristine (no errors, warnings)

**Test fails?** Fix code, not test.

**Other tests fail?** Fix now.

### REFACTOR — Clean Up

After green only:
- Remove duplication
- Improve names
- Extract helpers

Keep tests green. Don't add behavior.

### Repeat

Next failing test for next feature.

## Seams — Where Tests Go

A **seam** is the public boundary you test at: the interface where you observe
behavior without reaching inside. Tests live at seams, never against internals.

**Test only at pre-agreed seams.** Before writing any test, write down the
seams under test and confirm them with the user. No test is written at an
unconfirmed seam.

Ask: "What's the public interface, and which seams should we test?"

## Anti-Patterns

- **Implementation-coupled** — mocks internal collaborators, tests private
  methods, or verifies through a side channel. The tell: the test breaks when
  you refactor but behavior hasn't changed.
- **Tautological** — the assertion recomputes the expected value the way the
  code does (`expect(add(a, b)).toBe(a + b)`), so it passes by construction and
  can never disagree with the code. Expected values must come from an
  independent source of truth.
- **Horizontal slicing** — writing all tests first, then all implementation.
  Work in **vertical slices** instead — one test → one implementation → repeat.

## Common Rationalizations

| Excuse | Reality |
|--------|---------|
| "Too simple to test" | Simple code breaks. Test takes 30 seconds. |
| "I'll test after" | Tests written after pass immediately — which proves nothing. |
| "Tests after achieve same goals" | Tests-after answer "what does this do?"; tests-first answer "what should this do?" |
| "Already manually tested" | Manual testing is ad-hoc: no record, no re-run, easy to forget cases. |
| "Deleting X hours is wasteful" | Sunk cost fallacy — keeping code you can't trust is the waste. |
| "Keep as reference, write tests first" | You'll adapt it. That's testing after. Delete means delete. |
| "Need to explore first" | Fine. Throw away exploration, start with TDD. |
| "Test hard = design unclear" | Listen to test. Hard to test = hard to use. |
| "TDD will slow me down" | TDD IS the pragmatic path: catches bugs before commit, prevents regressions. |

## Red Flags — STOP and Start Over

- Code before test
- Test after implementation
- Test passes immediately
- Can't explain why test failed
- Tests added "later"
- Rationalizing "just this once"
- "I already manually tested it"
- "Tests after achieve the same purpose"
- "It's about spirit not ritual"
- "Keep as reference" or "adapt existing code"
- "Already spent X hours, deleting is wasteful"
- "TDD is dogmatic, I'm being pragmatic"
- "This is different because..."

**All of these mean: Delete code. Start over with TDD.**

## Verification Checklist

Before marking work complete:

- [ ] Every new function/method has a test
- [ ] Watched each test fail before implementing
- [ ] Each test failed for expected reason (feature missing, not typo)
- [ ] Wrote minimal code to pass each test
- [ ] All tests pass
- [ ] Output pristine (no errors, warnings)
- [ ] Tests use real code (mocks only if unavoidable)
- [ ] Edge cases and errors covered

Can't check all boxes? You skipped TDD. Start over.

## Expected Outputs
- A failing test committed first (or staged separately from implementation).
- Minimal implementation that passes the test.
- Both test tracks pass (unit + real system interaction, per
  `../../../references/tests/tests.md`).
- Clean, refactored code with no regressions.

## References
- `../../../references/tests/tests.md` — two-track testing strategy.
- `../../../workflows/execution/work_verification.md` — verification protocol.
- `../../../workflows/quality/fallback_policy.md` — fallback policy.
- `../../../CONVENTIONS.md` — naming and zero-state conventions.
- Adapted from: obra/superpowers `test-driven-development` and
  mattpocock/skills `tdd`.
