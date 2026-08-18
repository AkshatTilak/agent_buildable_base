---
version: 1.0.0
updated: 2026-08-18
id: tasks
links:
  - ../CONVENTIONS.md
  - ../references/references.md
  - ../workflows/execution/work_principle.md
  - ../workflows/execution/work_verification.md
---

# Standard Operating Procedure: Task Management & Hierarchy (`tasks.md`)

This document defines the strict hierarchical structure and lifecycle of all
actionable work within this project. Both the orchestration agents mapping out
the system and the execution agents writing the code must adhere to these
definitions to maintain alignment and prevent scope creep.

> **Workflow-first**: Before any task is created, the workflows and designs must
> exist (see `../design/design.md`). Tasks are derived from designs, never the
> other way around.

---

## 1. The Task Hierarchy

All project work is divided into four layers, represented by the subdirectories
within the `tasks/` folder:

### I. `goal/` (The North Star)
- **Purpose:** Defines the absolute final objective of the entire system.
- **Scope:** Contains a single, high-level document outlining what the
  completed, production-ready system looks like.
- **Completion State:** The Goal is only achieved when **all** underlying Base
  Tasks are successfully executed, verified, and integrated.

### II. `base/` (Architectural Milestones)
- **Purpose:** Defines the broad, foundational objectives of the project (the
  "What" and the "Why").
- **Scope:** Each Base Task represents a major system component (e.g.
  "Implement Inference Pipeline", "Set up Database & Storage", "Build User Auth
  Flow").
- **Constraints:** Base Tasks do NOT contain granular code steps. They exist to
  group, provide context, and define the acceptance criteria for a cluster of
  Subtasks.

### III. `sub/` (Granular Execution Units)
- **Purpose:** The actual, actionable work given to an execution agent (the
  "How").
- **Scope:** Highly specific, scoped implementations.
- **Linkage:** Every Subtask **must** explicitly link back to its parent Base
  Task, and to any older tasks/features it supersedes or extends.
- **Completion State:** When all Subtasks linked to a Base Task are marked
  `[x]`, the parent Base Task is considered complete.

### IV. `temp/` (Ad-Hoc & Scope Management)
- **Purpose:** A holding zone for spontaneous, out-of-scope issues discovered
  during the execution of a Subtask.
- **Scope:** Minor bug fixes, refactoring notes, or missing dependency flags
  that arise dynamically.
- **Workflow:** If an agent encounters an issue outside its current Subtask, it
  must log a quick `.md` file in `temp/` and immediately return to its primary
  assignment. The orchestration agent later reviews `temp/` and **escalates**
  items into formal Subtasks (in `sub/`) or into `references/issues/` (technical
  debt) as appropriate.

---

## 2. Task IDs & Frontmatter

Every task file carries a YAML frontmatter header. This is the **versioning and
traceability** mechanism for tasks.

```yaml
---
id: <unique_task_id>            # e.g. base_001, sub_042, goal_001, temp_007
version: 1.0.0                  # bumped on every edit
updated: YYYY-MM-DD
status: not_started             # not_started | in_progress | completed
parent: <parent_task_id>        # for sub/: the base task id; for base/: the goal id
depends_on: [<task_ids>]        # tasks that must complete first
links:                          # cross-links to features/design/references/older tasks
  - ../features/<feature>.md
  - ../design/workflows/<workflow>.md
  - ../references/structure/<schema>.md
  - supersedes: ../tasks/sub/<older_task>.md
---
```

- **`id`** is stable and never reused.
- **`version`** bumps on every edit; the changelog section below records what changed.
- **`links`** is how tasks stay connected to features, designs, references, and
  **older tasks/versions they supersede** — so history is never lost when a task
  changes something an older task already handled.

---

## 3. Task Definition Standards

Templates live in `tasks/_templates/` and are copied (not edited in place) when
creating a new task.

### Goal Template (`tasks/goal/goal.md`)
- **Objective:** One-sentence statement of the ultimate system objective.
- **Success Criteria:** The measurable definition of "done" for the whole system.
- **Base Task Registry:** Checklist of all Base Tasks required to achieve the Goal.
- **Constraints & Non-Goals:** What the system explicitly will NOT do.
- **User Decisions:** Recorded user choices that shaped the goal (see
  `../workflows/user/user_input.md`).

### Base Task Template (`tasks/base/[task_name].md`)
- **Objective:** A one-sentence summary of the milestone.
- **Business/System Value:** Why this component is necessary.
- **Subtask Registry:** A checklist of all linked subtasks required to complete
  this base task.
- **Complexity Rating:** Low / Medium / High (see rubric in §5).
- **Definition of Done:** Acceptance criteria for the whole milestone.

### Subtask Template (`tasks/sub/[subtask_name].md`)
- **Parent Link:** Explicit reference to `base/[task_name].md`.
- **Actionable Steps:** A strictly ordered checklist of technical steps.
- **Dependencies:** Required packages, environment variables, or other subtasks
  that must complete first.
- **Definition of Done:** Explicit criteria required before marking the subtask
  `[x]` and triggering the `work_verification.md` flow.
- **Edge Cases:** Explicitly listed edge cases that must be handled and tested.

### Temp Template (`tasks/temp/[note_name].md`)
- **Origin:** Which subtask/agent discovered this.
- **Description:** The out-of-scope issue.
- **Suggested Disposition:** escalate to `sub/` | log to `references/issues/` | ignore.

---

## 4. Execution State & Roll-Up Mechanics

Agents must actively maintain the state of the system by updating the checkboxes
inside the markdown files:

1. **Unassigned:** `[ ]`
2. **In Progress:** `[/]` (The agent is currently holding this context).
3. **Completed:** `[x]` (The code is written, verified via `work_verification.md`,
   and fully integrated).

**The Roll-Up Rule:** No Base Task can be marked `[x]` until 100% of its
registered Subtasks are verified and marked `[x]`. No Goal is met until all Base
Tasks are cleared.

---

## 5. Complexity Rubric

| Rating | Definition |
|--------|------------|
| **Low** | Single-file change; no external dependencies; no schema change; < ~1 day. |
| **Medium** | Multi-file change; touches one subsystem; may add a small schema/migration; 1–3 days. |
| **High** | Cross-subsystem; schema + migration + API changes; new infrastructure; > 3 days; needs a design doc. |

---

## 6. Ordering & Dependency Graph

- **Ordering** is expressed by numeric prefixes in `tasks/sub/`
  (`01_`, `02_`, ...) AND by the `depends_on` field in frontmatter.
- The `depends_on` field is the authoritative dependency graph; numeric prefixes
  are a convenience for reading order.
- **Renumbering rule:** when a task is inserted or removed, renumber the
  affected `sub/` files and update `depends_on` references. Do not leave gaps or
  out-of-order numbers.
- A task may only be marked `in_progress` when all its `depends_on` tasks are
  `completed`.

---

## 7. Changelog

- Every task file keeps a short changelog at the bottom recording notable edits
  (what changed, when, and why).
- The base-level changelog is `../CHANGELOG.md`; feature-level changelogs live
  in `../features/<feature>/CHANGELOG.md`.