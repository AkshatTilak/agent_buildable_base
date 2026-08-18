---
version: 1.0.0
updated: 2026-08-18
id: recheck_codebase
links:
  - ../../references/references.md
  - ../../tasks/tasks.md
  - ../../features/features.md
  - ../../CONVENTIONS.md
  - ../execution/work_verification.md
---

# Recheck Codebase — Audit For Drift & Missed Updates

> **Purpose**: Task workers sometimes forget to update references, tasks,
> changelogs, or features. This workflow audits the codebase for drift and fixes
> missed updates.

## 1. When To Use

- Periodically, or when a worker may have forgotten to update something.
- After a batch of tasks completes.
- Before a release or handoff.

## 2. What To Check

- **References**: are `references/` files up to date with the actual code?
- **Tasks**: are task statuses, checkboxes, and roll-ups correct?
- **Changelogs**: are `CHANGELOG.md` and feature changelogs updated?
- **Features**: do feature specs/diagrams match the implemented behavior?
- **Design**: do designs reflect the current system?
- **DB sync**: are schema, models, and migrations in sync (see
  `../../references/db/`)?
- **Conventions**: are snake_case, zero-state, and duplicate-name rules followed
  (see `../../CONVENTIONS.md`)?
- **Tooling**: is `STACK.md` accurate? Are lint/format/typecheck clean?
- **Logs**: is log bloat present (see `../../references/logs/`)?

## 3. Process

1. Scan the workspace for drift against the recorded state.
2. Produce a **drift report** listing what is out of date.
3. Fix the missed updates (references, tasks, changelogs, features).
4. Log any recurring pattern to `references/issues/` and evolve
   `../execution/work_principle.md` / `../../CODING_PHILOSOPHY.md` if needed.

## 4. Output

- A drift report.
- Corrected references/tasks/changelogs/features.
- Any new issues logged.
