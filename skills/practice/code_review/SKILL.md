---
version: 2.0.0
updated: 2026-08-18
id: skill_practice_code_review
links:
  - ../../../CONVENTIONS.md
  - ../../../CODING_PHILOSOPHY.md
  - ../../../workflows/execution/work_verification.md
  - ../../../workflows/quality/recheck_codebase.md
  - ../../../references/tests/tests.md
  - ../../../STACK.md
---

# Skill: Code Review

> **Purpose**: A structured two-axis code review that evaluates diffs against
> repository standards and feature specification. Adapted from
> mattpocock/skills `code-review` and obra/superpowers `requesting-code-review`.

## Trigger Conditions
- Load this skill when reviewing a code diff, pull request, or completed
  subtask.
- Load when `../../../workflows/execution/work_verification.md` triggers an
  audit.
- Load when the user requests a review.

## Two-Axis Review

A change can pass one axis and fail the other:

- Code that follows every standard but implements the wrong thing → **Standards pass, Spec fail.**
- Code that does exactly what the issue asked but breaks the project's conventions → **Spec pass, Standards fail.**

Reporting them separately stops one axis from masking the other.

## Process

### 1. Pin the Fixed Point

Whatever the user said is the fixed point — a commit SHA, branch name, tag,
`main`, `HEAD~5`, etc. If they didn't specify one, ask for it.

Capture the diff command once: `git diff <fixed-point>...HEAD` (three-dot, so
the comparison is against the merge-base). Also note the list of commits via
`git log <fixed-point>..HEAD --oneline`.

Before going further, confirm the fixed point resolves (`git rev-parse
<fixed-point>`) and the diff is non-empty.

### 2. Identify the Spec Source

Look for the originating spec, in this order:

1. Issue references in the commit messages (`#123`, `Closes #45`, etc.)
2. A path the user passed as an argument
3. A spec file under `docs/`, `specs/`, or `.scratch/` matching the branch name
4. If nothing is found, ask the user where the spec is

### 3. Identify the Standards Sources

Anything in the repo that documents how code should be written, such as
`CONVENTIONS.md`, `CODING_PHILOSOPHY.md`, or `CONTRIBUTING.md`.

On top of whatever the repo documents, the Standards axis always carries the
**smell baseline** — a fixed set of Fowler code smells that applies even when a
repo documents nothing. Two rules bind it:

- **The repo overrides.** A documented repo standard always wins; where it
  endorses something the baseline would flag, suppress the smell.
- **Always a judgement call.** Each smell is a labelled heuristic, never a hard
  violation — and skip anything tooling already enforces.

**Smell baseline:**
- **Mysterious Name** — a function, variable, or type whose name doesn't reveal
  what it does or holds. → rename it; if no honest name comes, the design's murky.
- **Duplicated Code** — the same logic shape appears in more than one hunk or
  file. → extract the shared shape, call it from both.
- **Feature Envy** — a method that reaches into another object's data more than
  its own. → move the method onto the data it envies.
- **Data Clumps** — the same few fields or params keep travelling together. →
  bundle them into one type, pass that.
- **Primitive Obsession** — a primitive standing in for a domain concept. →
  give the concept its own small type.
- **Repeated Switches** — the same `switch`/`if`-cascade on the same type
  recurs. → replace with polymorphism, or one map both sites share.
- **Shotgun Surgery** — one logical change forces scattered edits across many
  files. → gather what changes together into one module.
- **Divergent Change** — one file or module is edited for several unrelated
  reasons. → split so each module changes for one reason.
- **Speculative Generality** — abstraction added for needs the spec doesn't
  have. → delete it; inline back until a real need shows.
- **Message Chains** — long `a.b().c().d()` navigation. → hide the walk behind
  one method on the first object.
- **Middle Man** — a class or function that mostly just delegates onward. → cut
  it, call the real target direct.
- **Refused Bequest** — a subclass that ignores or overrides most of what it
  inherits. → drop the inheritance, use composition.

### 4. Standards Review

Evaluate the diff against repository standards:
- **Casing & naming**: snake_case per `../../../CONVENTIONS.md` §1; no
  duplicates per §3.
- **Zero-state convention**: every structure defines empty, populated, and
  errored states per `../../../CONVENTIONS.md` §2.
- **Fallback policy**: no blanket `try/except`, no hard pre-created fallbacks
  per `../../../workflows/quality/fallback_policy.md`.
- **DRY violations**: no duplicate logic; abstractions justified by the deletion
  test.
- **Code philosophy**: aligns with `../../../CODING_PHILOSOPHY.md`.
- **Tooling compliance**: linter, formatter, and type-checker pass cleanly.
- **Smell baseline**: check against the Fowler smells listed above.

### 5. Specification Review

Evaluate the diff against the task specification:
- **Completeness**: every item in the Definition of Done is addressed.
- **Correctness**: the implementation matches the spec's intent, not just its
  literal text.
- **Edge cases**: all edge cases listed in the task are covered.
- **No scope creep**: the diff does not include changes beyond the task's scope.

### 6. Report Findings

Present the two reports under `## Standards` and `## Spec` headings. Do **not**
merge or rerank findings — the two axes are deliberately separate.

Categorize findings: **blocker** (must fix before merge), **advisory**
(improvement, not blocking), **praise** (noteworthy good practice).

End with a one-line summary: total findings per axis, and the worst issue
_within each axis_ (if any).

## Anti-Patterns
- **Style nitpicking without substance**: focus on correctness, structure, and
  conventions — not personal style preferences.
- **Reviewing without the spec**: never review code without knowing what it was
  supposed to do.
- **Rubber-stamping**: every review must produce findings or an explicit
  "no issues found" with reasoning.
- **Merging the axes**: keep Standards and Spec separate — one can mask the
  other.

## Expected Outputs
- Categorized findings (blocker / advisory / praise) with citations.
- Confirmation that both standards and spec reviews passed.
- Signed-off diff ready for merge (or blockers documented for rework).

## References
- `../../../CONVENTIONS.md` — naming, zero-state, duplicate rules.
- `../../../CODING_PHILOSOPHY.md` — code philosophy.
- `../../../workflows/execution/work_verification.md` — verification protocol.
- `../../../workflows/quality/recheck_codebase.md` — drift audit.
- `../../../references/tests/tests.md` — testing strategy.
- `../../../STACK.md` — tooling.
- Adapted from: mattpocock/skills `code-review` and obra/superpowers
  `requesting-code-review`.
