# Version 3 (v3) Task SOP & Hierarchy (`tasks/v3/tasks.md`)

This document defines the hierarchical structure and lifecycle of all Version 3 (v3) system tasks.

---

## 1. The Task Hierarchy (v3)

All v3 project work is divided into four distinct layers within the `tasks/v3/` directory:

### I. `goal/` (The North Star)
* **Purpose:** Defines the absolute final objective of the v3 system.
* **Scope:** Contains `tasks/v3/goal/contained_v3_platform.md`.
* **Completion State:** The Goal is achieved when **all** underlying v3 Base Tasks are successfully executed, verified, and integrated.

### II. `base/` (Architectural Milestones)
* **Purpose:** Defines the broad, foundational objectives of the project (the "What" and the "Why").
* **Scope:** Each Base Task in `tasks/v3/base/` represents a major system component.
* **Constraints:** Base Tasks do NOT contain granular code steps. They exist to group, provide context, and define acceptance criteria for v3 Subtasks.

### III. `sub/` (Granular Execution Units)
* **Purpose:** The actual, actionable work given to an execution agent (the "How").
* **Scope:** Highly specific, scoped implementations in `tasks/v3/sub/`.
* **Linkage:** Every Subtask **must** explicitly link back to its parent Base Task in `tasks/v3/base/`.
* **Completion State:** When all Subtasks linked to a Base Task are marked `[x]`, the parent Base Task is considered complete.

### IV. `temp/` (Ad-Hoc & Scope Management)
* **Purpose:** A holding zone for spontaneous, out-of-scope issues discovered during execution of a v3 Subtask (`tasks/v3/temp/`).

---

## 2. V3 Focus Areas

V3 is a **polish, usability, and production-readiness** release with 8 base tasks:

| # | Base Task | Focus Area | Complexity |
|---|---|---|---|
| 01 | Bugfixes & Breaking Flows | Fix all identified v2 issues and code quality gaps | High |
| 02 | Frontend Design System | Establish authoritative design token system and component patterns | Medium |
| 03 | Frontend Premium UI Overhaul | Transform the frontend into a premium control plane experience | High |
| 04 | Frontend Routing & Navigation | Add React Router, page transitions, command palette | Medium |
| 05 | Frontend State & Data Layer | New store slices, TypeScript types, settings persistence | Medium |
| 06 | Ingestion & Documents UX | Drag-drop, job tracking, batch upload, document library | High |
| 07 | Docker Infrastructure (16GB RAM) | RAM-optimized profiles, dev overrides, memory limits | Medium |
| 08 | Reference & Documentation Sync | Update all references for v3, update agent.md, README | Low |

---

## 3. Execution State & Roll-Up Mechanics

Agents must actively maintain the state of the system by updating the checkboxes inside the markdown files:

1. **Unassigned:** `[ ]`
2. **In Progress:** `[/]` (The agent is currently holding this context).
3. **Completed:** `[x]` (The code is written, verified, and fully integrated).

**The Roll-Up Rule:** No Base Task can be marked `[x]` until 100% of its registered Subtasks are verified and marked `[x]`. No Goal is met until all Base Tasks are cleared.
