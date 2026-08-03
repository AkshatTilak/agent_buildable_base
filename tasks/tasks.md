# Standard Operating Procedure: Task Management & Version Hierarchy (`tasks.md`)

This document defines the strict hierarchical structure, versioning lifecycle, and roll-up mechanics of all actionable work within this project. Both system architects and execution agents must adhere to these versioning definitions to maintain operational alignment across release cycles.

---

## 1. Task Versioning & Organization

All project tasks are organized by release or milestone versions into version subdirectories under `tasks/` (e.g., `tasks/v1/`, `tasks/v2/`, ...).

Each version directory (`tasks/v<N>/`) represents a distinct development phase or feature milestone cycle. Within every version directory, work follows the standard 4-layer task hierarchy:

```text
tasks/
├── tasks.md                    # 📖 Master task guide & version registry (this file)
├── v1/                         # Version 1 (Baseline Buildable Base - Completed)
│   ├── tasks.md                # v1 Task Registry & roll-up SOP
│   ├── goal/                   # v1 North Star goal document
│   ├── base/                   # v1 Architectural milestone tasks
│   ├── sub/                    # v1 Granular execution subtasks
│   └── temp/                   # v1 Ad-hoc holding zone
└── v2/                         # Version 2 (Improvements, Bugfixes & Enhancements - Active)
    ├── tasks.md                # v2 Task Registry & roll-up SOP
    ├── goal/                   # v2 North Star goal document
    ├── base/                   # v2 Architectural milestone tasks
    ├── sub/                    # v2 Granular execution subtasks
    └── temp/                   # v2 Ad-hoc holding zone
```

---

## 2. The Task Hierarchy (per Version)

Within any active version directory (`tasks/v<N>/`), work is divided into four distinct layers:

### I. `goal/` (The North Star)
* **Purpose:** Defines the absolute final objective of this version's milestone.
* **Scope:** Contains a single, high-level document outlining what the completed version looks like.
* **Completion State:** Achieved when **all** underlying Base Tasks for this version are successfully executed, verified, and integrated.

### II. `base/` (Architectural Milestones)
* **Purpose:** Defines the broad, foundational objectives for the version (the "What" and "Why").
* **Scope:** Each Base Task represents a major component or feature group (e.g., "Implement Inference Pipeline", "Refactor Model Hub").
* **Constraints:** Base Tasks exist to group, provide context, and define the acceptance criteria for a cluster of Subtasks.

### III. `sub/` (Granular Execution Units)
* **Purpose:** The actual, actionable work given to an execution agent (the "How").
* **Scope:** Highly specific, scoped implementations.
* **Linkage:** Every Subtask **must** explicitly link back to its parent Base Task.
* **Completion State:** When all Subtasks linked to a Base Task are marked `[x]`, the parent Base Task is considered complete.

### IV. `temp/` (Ad-Hoc & Scope Management)
* **Purpose:** A holding zone for spontaneous, out-of-scope issues discovered during execution of a Subtask.
* **Workflow:** If an agent encounters an issue outside its current Subtask, it logs a `.md` file in `tasks/v<N>/temp/` and returns to its primary assignment.

---

## 3. Version Registry & Roll-Up Mechanics

### Active & Past Version Index

| Version | Status | Focus / Description | Folder Path |
|---|---|---|---|
| **v1** | `Completed [x]` | Base Buildable Architecture & Initial Core Modules (Common Lib, SyntraFlow, GuardRoute, Inference, EvalOps, Gateway, Infrastructure, Security) | [`tasks/v1/`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v1) |
| **v2** | `Completed [x]` | Granular Real-Time Frontend, Visual Workflow Builder, Dynamic Ingestion Pipelines, Agent Management (CRUD), and Eval Test Generation | [`tasks/v2/`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v2) |
| **v3** | `Completed [x]` | System-wide Polish, Design Tokens & Premium UI, RAM-Optimized Infrastructure (16GB), React Router, Type Safety & Bugfixes | [`tasks/v3/`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v3) |
| **v4** | `Completed [x]` | Frontend Spacing, Padding & Typography Overhaul — Fix text truncation, declutter microservice grids & cards | [`tasks/v4/`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v4) |
| **v5** | `Completed [x]` | Platform Maturity — EvalOps Deep Integration (RAGAS+DeepEval), Agent Endpoints & Routing, User Auth & RBAC, Model Playground, MCP Integration Hub, Workflow Builder Enhancements, External API Gateway | [`tasks/v5/`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5) |
| **v6** | `Completed [x]` | The Hub Platform — Multi-tenant hub model (Ingestion, Agent, Workflow, Eval), RBAC v2, User Management & Invites, Multi-Workflow Builder, Polymorphic Evals, Hub Shell UI | [`tasks/v6/`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6) |
| **v7** | `Active [/]` | Platform Stabilization & Lifecycle — Full user lifecycle (soft/hard delete), Logout & session revocation, Gateway fail-fast & logging, `.env` super admin bootstrapping, Docker local host path volumes, Clean startup empty states, Infrastructure UI dependency rendering, Full API Test Suite, and Reference System Audit | [`tasks/v7/`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v7) |

### Version Management Rules

1. **Active Version Context:** Agents MUST operate within the designated active version directory (`tasks/v<N>/`). Do not modify completed version directories unless explicitly instructed for historical backfills.
2. **Roll-Up Rule:** A version's Goal (`tasks/v<N>/goal/`) is met only when 100% of its Base Tasks (`tasks/v<N>/base/`) and Subtasks (`tasks/v<N>/sub/`) are marked `[x]`.
3. **Transitioning Versions:** When a version's goal is complete, the System Architect creates the next version folder (`v<N+1>`), establishes the new `goal/`, `base/`, and `sub/` tasks, and updates this master registry.

