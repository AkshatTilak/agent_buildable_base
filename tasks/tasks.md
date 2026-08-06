# Standard Operating Procedure: Task Management & Hierarchy (`tasks.md`)

This document defines the strict hierarchical structure and lifecycle of all actionable work within this project. Both the orchestration agents mapping out the system and the execution agents writing the code must adhere to these definitions to maintain alignment and prevent scope creep.

---

## 1. The Task Hierarchy

All project work is divided into four distinct layers, represented by the subdirectories within the `tasks/` folder:

### I. `goal/` (The North Star)
* **Purpose:** Defines the absolute final objective of the entire system.
* **Scope:** Contains a single, high-level document outlining what the completed, production-ready system looks like.
* **Completion State:** The Goal is only achieved when **all** underlying Base Tasks are successfully executed, verified, and integrated.
* All goal tasks should have [ ] checkboxes to track them and link to their specific subtask or refrences.
* Each new goal should be versioned starting from v1 (folder wise manage and create a version folder under which all tasks, goals, subtasks, temp tasks should be maintained).

### II. `base/` (Architectural Milestones)
* **Purpose:** Defines the broad, foundational objectives of the project (the "What" and the "Why").
* **Scope:** Each Base Task represents a major system component (e.g., "Implement Inference Pipeline", "Set up Database & Storage", "Build User Auth Flow").
* **Constraints:** Base Tasks do NOT contain granular code steps. They exist to group, provide context, and define the acceptance criteria for a cluster of Subtasks.

### III. `sub/` (Granular Execution Units)
* **Purpose:** The actual, actionable work given to an execution agent (the "How").
* **Scope:** Highly specific, scoped implementations (e.g., "Write Pydantic schemas for User model", "Configure Docker-compose for Postgres", "Implement LangChain API wrapper").
* **Linkage:** Every Subtask **must** explicitly link back to its parent Base Task.
* **Completion State:** When all Subtasks linked to a Base Task are marked `[x]`, the parent Base Task is considered complete.

### IV. `temp/` (Ad-Hoc & Scope Management)
* **Purpose:** A holding zone for spontaneous, out-of-scope issues discovered during the execution of a Subtask.
* **Scope:** Minor bug fixes, refactoring notes, or missing dependency flags that arise dynamically.
* **Workflow:** If an agent encounters an issue outside its current Subtask, it must log a quick `.md` file in `temp/` and immediately return to its primary assignment. The orchestration agent will later review `temp/` to elevate these items into formal Subtasks if necessary.

---

## 2. Task Definition Standards

When the Architect Agent generates task files, it must follow these templates:

### Base Task Template (`tasks/base/[task_name].md`)
* **Objective:** A one-sentence summary of the milestone.
* **Business/System Value:** Why this component is necessary.
* **Subtask Registry:** A checklist of all linked subtasks required to complete this base task.
  * `[ ] sub/01_database_setup.md`
  * `[ ] sub/02_schema_definitions.md`
* **Complexity Rating:** Estimated architectural weight (e.g., Low, Medium, High).

### Subtask Template (`tasks/sub/[subtask_name].md`)
* **Parent Link:** Explicit reference to `base/[task_name].md`.
* **Actionable Steps:** A strictly ordered checklist of technical steps.
  * `[ ] Step 1: Initialize clients...`
  * `[ ] Step 2: Write tests...`
* **Dependencies:** Required packages, environment variables, or other subtasks that must be completed first.
* **Definition of Done:** Explicit criteria required before marking the subtask `[x]` and triggering the `work_verification.md` flow.

---

## 3. Execution State & Roll-Up Mechanics

Agents must actively maintain the state of the system by updating the checkboxes inside the markdown files:

1. **Unassigned:** `[ ]` 
2. **In Progress:** `[/]` (The agent is currently holding this context).
3. **Completed:** `[x]` (The code is written, verified via `work_verification.md`, and fully integrated).

**The Roll-Up Rule:** No Base Task can be marked `[x]` until 100% of its registered Subtasks are verified and marked `[x]`. No Goal is met until all Base Tasks are cleared.