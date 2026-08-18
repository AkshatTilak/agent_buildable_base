# Agentic Workspace: Autonomous System Builder

Welcome to the Agentic Workspace. This repository is an AI-driven, highly
modular framework designed to autonomously architect, execute, and verify
complete software systems from a raw user idea.

It is **stack-agnostic**: the stack, tooling, OS, and shell are detected or
asked at init time and recorded in `STACK.md`.

---

## 1. Core Architecture & AI Services

This system is built with AI orchestration as a first-class citizen. The
intelligence layer drives all downstream execution.

### Agent Orchestration Layer
- **High-Level System Architect (`agent.md`):** The primary orchestrator. It
  digests requirements, maps the high-level topology, and bifurcates the work
  into actionable milestones.
- **Execution & Inference Agents:** Specialized agents that read isolated tasks,
  fetch necessary context, and write the core business logic.
- **Verification & Guardrail Agents:** Autonomous auditing agents that validate
  inference integrity, token efficiency, and code structure before marking a
  task complete.

### AI Integration & Observability
- **Inference Integrity:** Built-in hooks for LLM configurations, prompt
  management, and evaluation benchmarks.
- **Contextual Memory (`references/`):** A decoupled memory bank that prevents
  context window pollution.

---

## 2. Backend & Infrastructure Foundations

Supporting the AI orchestration layer is a robust, production-ready backend
designed for scalability.
- **Modular Subsystems:** Shared utilities, data models, and configurations live
  in a centralized library to enforce DRY principles.
- **Data & Type Safety:** Heavy utilization of explicit typing and validation.
- **Containerization & Deployment:** Native support for isolated Docker
  environments, with an optimization checklist to keep images lean.

---

## 3. Workspace Directory Structure

```text
├── agent.md                # Base Architect prompt — always loaded, references workflows/router.md
├── README.md               # You are here
├── CHANGELOG.md            # Base-level changelog
├── VERSION                 # Base version (semver)
├── STACK.md                # Recorded stack, tooling, OS, shell for THIS project
├── USER_PREFERENCES.md     # Per-user preferences (tools, casing, fallback stance, verbosity)
├── CONVENTIONS.md          # snake_case, zero-state, duplicate-name, fallback rules
├── CODING_PHILOSOPHY.md    # How we write code
├── workflows/              # Workflows (renamed from prompts/) — organized by category
│   ├── router.md           # Routes user prompts to the best workflow (checked FIRST)
│   ├── execution/          # work_principle, work_verification
│   ├── planning/           # planning, init_project, extend_goal, extend_task
│   ├── quality/            # recheck_codebase, fallback_policy
│   ├── user/               # user_input, find_skills
│   └── ci/                 # setup_ci
├── design/                 # All design kept separate
│   ├── design.md           # Master design guide & index
│   ├── system/             # System architecture designs
│   ├── workflows/          # Workflow designs (designed BEFORE tasks)
│   └── ux/                 # User-interaction / UX designs
├── features/               # Feature specs
│   ├── features.md         # Master feature guide & index
│   └── _template/          # Feature template (mermaid, connections, caveats, changelog)
├── references/             # The contextual memory bank for agents
│   ├── references.md       # Master guide for reading/writing contextual memory
│   ├── code/               # Explanations of complex logic and historical mappings
│   ├── deployment/         # Dockerfiles, CI/CD, env templates, optimization checklist
│   ├── issues/             # Technical debt, bottlenecks, and blocked tasks
│   ├── logic/              # Business rules and system interaction flows
│   ├── resource/           # External API docs, dataset links, and asset catalogs
│   ├── structure/          # Database schemas, API routing, and dependency graphs
│   ├── user/               # Human-in-the-loop requests (API keys, manual QA)
│   ├── tests/              # Two-track testing conventions
│   ├── logs/               # Logging, tracing, and observability conventions
│   ├── db/                 # Schema/model/migration sync tracking
│   └── tooling/            # Linter/formatter/type-checker/dep-manager + OS/shell
├── tasks/                  # Active execution directory
│   ├── tasks.md            # Master guide for task hierarchy and state roll-ups
│   ├── _templates/         # Reusable Goal/Base/Sub/Temp skeletons
│   ├── goal/               # The ultimate system objective
│   ├── base/               # Broad architectural milestones (The "What" and "Why")
│   ├── sub/                # Granular execution units (The "How")
│   └── temp/               # Holding zone for spontaneous, out-of-scope issues
├── skills/                 # Reusable job skills (tracked) — folder-per-skill
│   ├── skills.md           # Skills index & how to write a skill
│   ├── manage_skills.md    # Create/update/delete skills (CRUD)
│   ├── qa/backend/         # SKILL.md + scripts/
│   ├── qa/frontend/        # SKILL.md + scripts/
│   ├── qa/docker/          # SKILL.md + scripts/
│   ├── qa/network/         # SKILL.md + scripts/
│   ├── ui/creation/        # SKILL.md + scripts/
│   └── debug/traceback/    # SKILL.md + scripts/
└── assets/                 # User-provided test inputs (gitignored)
    └── README.md           # Explains purpose; contents are gitignored
```

---

## 4. How To Use

1. **Route** every interaction through `workflows/router.md` — it reads the user
   prompt and picks the best workflow.
2. **Start a project** with `workflows/planning/init_project.md` — either map an
   existing codebase (read-only analysis first) or bootstrap a new one.
3. **Design workflows first** (`design/`), then define features (`features/`),
   then decompose into tasks (`tasks/`).
4. **Execute** tasks per `workflows/execution/work_principle.md`.
5. **Verify** every completed task per `workflows/execution/work_verification.md`.
6. **Respect user preferences** (`USER_PREFERENCES.md`) and project overrides
   (`STACK.md`).
7. **Keep everything versioned** — bump versions and update changelogs.

---

## 5. Versioning

- The base version is in `VERSION` and `CHANGELOG.md`.
- Every file carries a YAML frontmatter header (`version`, `updated`, `id`,
  `links`).
- Releases are tagged in git (`v1.0.0`, ...).
