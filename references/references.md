---
version: 1.1.0
updated: 2026-08-20
id: references
links:
  - ../tasks/tasks.md
  - ../design/design.md
  - ../CONVENTIONS.md
  - ../workflows/execution/work_principle.md
  - ../workflows/execution/work_verification.md
---

# Reference Management & Contextual Memory (`references.md`)

This directory serves as the centralized knowledge base and memory bank for all
AI agents and human developers working on the project. It stores architectural
decisions, codebase context, unresolved issues, deployment guidelines, testing
and logging conventions, database/schema state, tooling, and human-in-the-loop
tasks.

**Role in the DDS** (see `../design/design.md` §2): `references/` holds the
*factual detail* that backs both design levels — HLD draws on `structure/`,
`db/`, `tooling/`, `deployment/`; LLD draws on `logic/`, `code/`, `tests/`,
`logs/`; `issues/`, `resource/`, `user/` are cross-cutting. Design docs
narrate; reference files record facts. **Link, don't copy.**

**Agent Instruction:** Before executing a task, query the relevant subdirectories
here to understand existing paradigms. Upon completing a complex task, updating
these reference files is mandatory to ensure downstream agents retain context.

---

## 1. Directory Structure & Usage Protocol

All contextual knowledge must be strictly categorized into the following
subdirectories. Do not dump raw data into the root folder.

### `logic/` (Business Rules & System Flow)
- **Purpose:** Stores the core business logic, decision matrices, and AI/system
  interaction flows.
- **Contents:** State machine definitions, prompt engineering strategies, core
  algorithmic rules, and data validation requirements.
- **When to use:** Read this to understand *why* a feature behaves the way it
  does. Update this when altering fundamental system behavior.

### `structure/` (Architecture & Topology)
- **Purpose:** Defines the physical and logical layout of the system.
- **Contents:** Database schemas, API routing maps, dependency graphs, and
  monorepo/folder architecture diagrams.
- **When to use:** Read this when creating new modules to ensure they fit the
  existing topology. Update this when adding new tables, microservices, or core
  integrations.

### `code/` (Codebase Explanations & Mappings)
- **Purpose:** Explains complex implementations that are not immediately obvious
  from reading the raw source code.
- **Contents:** File-to-feature mappings, breakdowns of complex algorithms,
  custom class wrappers, and historical context on refactored changes.
- **When to use:** Query this when interacting with legacy code or highly
  abstracted wrappers.

### `deployment/` (Infrastructure & Execution)
- **Purpose:** Contains all commands and configurations required to run, test,
  and deploy the system, **including an optimization checklist**.
- **Contents:** Docker commands, CI/CD pipeline structures, environment variable
  templates (`.env.example`), server setup scripts, and the deployment
  optimization checklist (multi-stage builds, cache cleanup, image size).
- **When to use:** Use this to provide the user with exact copy-paste commands
  to spin up test environments or production builds. Always apply the
  optimization checklist before finalizing deployment files.

### `issues/` (Technical Debt & Blockers)
- **Purpose:** Tracks known bugs, architectural bottlenecks, and unresolved
  system flaws.
- **Contents:** Post-mortems of failed integrations, notes on deprecated
  libraries, and edge-cases that models were unable to rectify.
- **When to use:** Check this before starting a task to ensure you aren't
  building on top of a broken dependency. Add to this if a task is blocked by
  external factors. Escalated `tasks/temp/` items land here.

### `resource/` (External Knowledge & Assets)
- **Purpose:** Catalogs external tools, documentation, and assets relied upon by
  the project.
- **Contents:** Links to specific versions of API docs, required datasets, or
  third-party service limits.
- **When to use:** Reference this to verify library capabilities before
  hallucinating non-existent functions.

### `user/` (Human-in-the-Loop Actions)
- **Purpose:** Defines tasks, approvals, and configurations that *only* the
  human developer can execute.
- **Contents:** Requests for API keys, billing setup instructions, manual QA
  testing requirements, or UI/UX visual approvals.
- **When to use:** If an agent reaches a hard boundary (e.g., needing an API key
  or a deployment token), log the requirement here and halt that execution path.

### `tests/` (Testing Conventions & Locations)
- **Purpose:** Defines the two-track testing strategy and where tests live.
- **Contents:** Unit test conventions, real system interaction / feature &
  workflow test conventions, edge-case coverage mandate, and test log locations.
- **When to use:** Read before writing tests. Update when the testing strategy
  changes.

### `logs/` (Logging, Tracing & Observability)
- **Purpose:** Defines how logs and traces are produced, stored, and found.
- **Contents:** Docker logs, dev logs, failure logs, and test logs — locations,
  formats, retention, and the warning/bloat cleanup policy.
- **When to use:** Read before adding logging. Update when log locations or
  formats change.

### `db/` (Database, Models & Migrations)
- **Purpose:** Keeps the database schema, models, and migrations in sync.
- **Contents:** Schema definitions, model/schema view files (human-readable),
  and migration tracking.
- **When to use:** Read before any schema/model change. Update whenever a schema
  or migration changes so they never drift.

### `tooling/` (Tooling, OS & Shell)
- **Purpose:** Records the chosen linter, formatter, type-checker, and dependency
  manager, plus OS/shell-specific command variants.
- **Contents:** How each tool is invoked, where config lives, and OS/shell
  variants (powershell/bash/cmd/zsh).
- **When to use:** Read before running any command or generating scripts. Update
  when tooling or environment changes.

---

## 2. Read / Write Guidelines for Agents

- **Search Before You Build:** Always perform a workspace search within this
  `references/` directory before writing new core logic to prevent duplicating
  existing structures.
- **Keep It Modular:** Create isolated `.md` files within the subdirectories for
  specific topics (e.g., `structure/database_schema.md`,
  `deployment/docker_setup.md`).
- **Timestamp & Link:** When noting an issue or code explanation, link back to
  the exact task or requirement file that generated it, and provide a rough
  timestamp or version number for traceability.
- **Frontmatter:** Every reference file carries the standard frontmatter
  (`version`, `updated`, `id`, `links`).
- **Index:** Keep the index in §3 up to date so references stay discoverable.

---

## 3. Reference Index

> Add new reference files to this index as they are created.

| Area | Path | Status |
|------|------|--------|
| _(none yet)_ | | |

---

## 4. Changelog

- `1.1.0` (2026-08-20): Declared the HLD/LLD support role within the DDS;
  added anti-duplication rule (link, don't copy).
- `1.0.0` (2026-08-18): Initial reference management guide.
