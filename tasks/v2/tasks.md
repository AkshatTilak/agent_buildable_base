# Version 2 (v2) Task SOP & Hierarchy (`tasks/v2/tasks.md`)

This document defines the hierarchical structure and lifecycle of all Version 2 (v2) system tasks.

---

## 1. The Task Hierarchy (v2)

All v2 project work is divided into four distinct layers within the `tasks/v2/` directory:

### I. `goal/` (The North Star)
* **Purpose:** Defines the absolute final objective of the v2 system.
* **Scope:** Contains `tasks/v2/goal/contained_v2_platform.md`.
* **Completion State:** The Goal is achieved when **all** underlying v2 Base Tasks are successfully executed, verified, and integrated.

### II. `base/` (Architectural Milestones)
* **Purpose:** Defines the broad, foundational objectives of the project (the "What" and the "Why").
* **Scope:** Each Base Task in `tasks/v2/base/` represents a major system component (e.g., Granular Frontend, Visual Workflow Builder, Dynamic Ingestion, Agent CRUD).
* **Constraints:** Base Tasks do NOT contain granular code steps. They exist to group, provide context, and define acceptance criteria for v2 Subtasks.

### III. `sub/` (Granular Execution Units)
* **Purpose:** The actual, actionable work given to an execution agent (the "How").
* **Scope:** Highly specific, scoped implementations in `tasks/v2/sub/`.
* **Linkage:** Every Subtask **must** explicitly link back to its parent Base Task in `tasks/v2/base/`.
* **Completion State:** When all Subtasks linked to a Base Task are marked `[x]`, the parent Base Task is considered complete.

### IV. `temp/` (Ad-Hoc & Scope Management)
* **Purpose:** A holding zone for spontaneous, out-of-scope issues discovered during execution of a v2 Subtask (`tasks/v2/temp/`).

---

## 2. Task Definition Standards

When task files are created for v2, they follow these templates:

### Base Task Template (`tasks/v2/base/[task_name].md`)
* **Objective:** A one-sentence summary of the milestone.
* **Business/System Value:** Why this component is necessary.
* **Subtask Registry:** A checklist of all linked subtasks required to complete this base task.
  * `[ ] sub/01_frontend_setup.md`
  * `[ ] sub/02_api_integration.md`
* **Complexity Rating:** Estimated architectural weight (e.g., Low, Medium, High).

### Subtask Template (`tasks/v2/sub/[subtask_name].md`)
* **Parent Link:** Explicit reference to `v2/base/[task_name].md`.
* **Actionable Steps:** A strictly ordered checklist of technical steps.
* **Dependencies:** Required packages, environment variables, or other subtasks that must be completed first.
* **Definition of Done:** Explicit criteria required before marking the subtask `[x]` and triggering the verification flow.

---

## 3. Execution State & Roll-Up Mechanics

Agents must actively maintain the state of the system by updating the checkboxes inside the markdown files:

1. **Unassigned:** `[ ]` 
2. **In Progress:** `[/]` (The agent is currently holding this context).
3. **Completed:** `[x]` (The code is written, verified, and fully integrated).

**The Roll-Up Rule:** No Base Task can be marked `[x]` until 100% of its registered Subtasks are verified and marked `[x]`. No Goal is met until all Base Tasks are cleared.
