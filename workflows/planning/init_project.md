---
version: 1.0.0
updated: 2026-08-18
id: init_project
links:
  - ../../STACK.md
  - ../../USER_PREFERENCES.md
  - ../../tasks/tasks.md
  - ../../references/references.md
  - ../../design/design.md
  - ../../features/features.md
  - ../user/user_input.md
  - ../ci/setup_ci.md
---

# Init Project — Start A Project

> **Purpose**: The entry point for starting a project. It either **(a) maps an
> EXISTING codebase** into this system, or **(b) bootstraps a NEW project**'s
> goals, setup, and implementation.

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
   - What maps to `references/` (structure, logic, code, deployment, db, tests,
     logs, tooling).
   - What maps to `features/` (existing capabilities).
   - What maps to `tasks/` (goal, base, sub, temp).
   - What belongs in `design/`.
3. **Present** the proposed mapping to the user for approval.
4. **On approval**, populate the files and fill `../../STACK.md`.

## 3. Mode B — Bootstrap A NEW Project

1. **Clarify** the idea with the user (see `../user/user_input.md`).
2. **Design workflows first** (see `../../design/design.md`) — before tasks.
3. **Define the Goal** (`tasks/goal/`).
4. **Decompose** into Base Tasks and Subtasks (see `../../tasks/tasks.md`).
5. **Define features** (see `../../features/features.md`).
6. **Set up** the project skeleton (folders, dependency manager, tooling config).
7. **Set up CI** (see `../ci/setup_ci.md`).

## 4. Output

- `../../STACK.md` filled in.
- `references/`, `features/`, `design/`, `tasks/` populated (Mode A after
  approval; Mode B as part of bootstrap).
- `.gitignore` per-stack blocks appended.
- CI workflows (see `../ci/setup_ci.md`).
