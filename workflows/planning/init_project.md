---
version: 1.1.0
updated: 2026-08-20
id: init_project
links:
  - ../../STACK.md
  - ../../USER_PREFERENCES.md
  - ../../tasks/tasks.md
  - ../../tasks/goal/goal.md
  - ../../references/references.md
  - ../../design/design.md
  - ../../features/features.md
  - ./extend_goal.md
  - ../user/user_input.md
  - ../ci/setup_ci.md
---

# Init Project — Start A Project

> **Purpose**: The entry point for starting a project. It either **(a) maps an
> EXISTING codebase** into this system, or **(b) bootstraps a NEW project**'s
> SRS, design, setup, and implementation.

## 1. Detect / Ask The Environment

Before anything else, detect or ask (see `../user/user_input.md`):
- **Stack**: language(s), framework(s), runtime.
- **Tooling**: linter, formatter, type-checker, dependency manager.
- **OS**: Windows / Linux / macOS.
- **Shell**: powershell / bash / cmd / zsh.

Record all of this in `../../STACK.md` and `../../references/tooling/`.
Consult `../../USER_PREFERENCES.md` for user-level defaults.

## 2. Mode A — Map An EXISTING Codebase (READ-ONLY ANALYSIS FIRST)

> **Important**: This mode is **read-only analysis first**. It scans and proposes
> how the existing codebase fits this system. It does NOT write any
> `references/`, `tasks/`, or `features/` files until the user approves the
> mapping.

1. **Scan** the existing codebase: structure, entry points, modules, tests,
   deployment files, dependencies.
2. **Propose** a mapping:
   - What the SRS (`tasks/goal/goal.md`) requirements (`FR`/`NFR`/`IR`) are,
     derived from observed capabilities.
   - What maps to `references/` (structure, logic, code, deployment, db, tests,
     logs, tooling).
   - What maps to `features/` (existing capabilities → LLD specs).
   - What maps to `tasks/` (goal SRS, base, sub, temp) with `srs_refs`
     traceability.
   - What belongs in `design/` (HLD in `design/system/`, LLD workflows in
     `design/workflows/`).
3. **Present** the proposed mapping to the user for approval.
4. **On approval**, populate the files and fill `../../STACK.md`.

## 3. Mode B — Bootstrap A NEW Project

1. **Clarify** the idea with the user (see `../user/user_input.md`).
2. **Author the SRS** (`tasks/goal/goal.md` from `tasks/_templates/goal.md`):
   purpose, scope, constraints, and the first `FR`/`NFR`/`IR` requirements.
   Record user decisions in SRS §8.
3. **Design the DDS** (see `../../design/design.md`) — HLD (`design/system/`)
   and LLD (`design/workflows/`, `features/`) — BEFORE tasks.
4. **Decompose** into Base Tasks and Subtasks (see `../../tasks/tasks.md`),
   each carrying `srs_refs` back to the requirements it satisfies.
5. **Set up** the project skeleton (folders, dependency manager, tooling config).
6. **Set up CI** (see `../ci/setup_ci.md`).

## 4. Output

- `../../STACK.md` filled in.
- SRS authored; `design/`, `features/`, `references/`, `tasks/` populated
  (Mode A after approval; Mode B as part of bootstrap).
- `.gitignore` per-stack blocks appended.
- CI workflows (see `../ci/setup_ci.md`).

## 5. Changelog

- `1.1.0` (2026-08-20): Mode A/B now author the SRS first and decompose with
  `srs_refs` traceability; DDS terminology for design/features.
- `1.0.0` (2026-08-18): Initial init-project workflow.
