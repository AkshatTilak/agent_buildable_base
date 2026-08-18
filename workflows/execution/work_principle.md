---
version: 1.0.0
updated: 2026-08-18
id: work_principle
links:
  - ../../tasks/tasks.md
  - ../../references/references.md
  - ../../CONVENTIONS.md
  - ../../CODING_PHILOSOPHY.md
  - ../../STACK.md
  - ../../USER_PREFERENCES.md
  - work_verification.md
---

# Standard Operating Procedure: Task Execution & Principles (`work_principle.md`)

This document outlines the strict protocol and guidelines for all agents and
developers when selecting, executing, integrating, and documenting tasks within
this workspace.

---

## 1. Task Selection Limits & Prioritization

- **Max Active Load:** You may select and work on **no more than 3 tasks**
  simultaneously to maintain deep context and execution quality.
- **Dependency First Prioritization:** Work must be sequenced to unblock the
  most critical system components first, honoring the `depends_on` graph in
  `../../tasks/tasks.md` §6:
  1. **AI & Core Services:** Prioritize AI model integrations, prompt
     engineering structures, inference pipelines, and core agent architectures.
  2. **Backend & Infrastructure:** Following AI services, prioritize foundational
     backend tasks (e.g., database schemas, shared utilities, Docker setups, and
     message brokers).
  3. **Upstream Submodules:** Only after the AI and backend foundations are
     stable should work begin on downstream dependencies or UI wrappers.

---

## 2. Core Execution Principles

### Context Before Code (Adhere to `../../references/references.md`)
- **Query Memory First:** Before implementing complex logic, you **must** check
  the `references/` directory (e.g., `references/logic/`, `references/structure/`,
  `references/tests/`, `references/logs/`, `references/db/`) to ensure your
  implementation aligns with the Architect's systemic vision.
- **Update the Bank:** If you make a significant design choice, immediately
  document it in the appropriate `references/` subfolder.

### Environment & Tooling Awareness (Adhere to `../../STACK.md`)
- **Consult `STACK.md`** before generating any command, script, or path.
- Generate commands for the recorded **OS and shell** (powershell/bash/cmd/zsh).
- Use the recorded **linter, formatter, type-checker, and dependency manager**
  (see `../../references/tooling/`). Run them as part of delivery.
- **Consult `USER_PREFERENCES.md`** for user-level preferences (tools, casing,
  fallback stance, verbosity) that may affect how you work.

### Complete & Integrated Delivery
- Every task must be completed **fully and end-to-end**, integrating across all
  dependent submodules.
- **Never implement duplicate code.** Abstract recurring logic into a shared
  utility or `common/` package. Follow the duplicate-name root-causing rule in
  `../../CONVENTIONS.md` §3.

---

## 3. Scope Management & Tracking (Adhere to `../../tasks/tasks.md`)

### Out-of-Scope Issues & The `temp/` Directory
If you encounter a bug, missing dependency, or design flaw that falls **beyond
the scope of your current Subtask**:
  1. Do **NOT** attempt to resolve it silently.
  2. Following `tasks.md`, create a temporary markdown log in `tasks/temp/`.
  3. Immediately return focus solely to your assigned Subtask.

### Status Roll-Ups
Always update the status of checklist items (`[ ]` to `[/]` to `[x]`). Remember
the Roll-Up Rule: A Base Task is only complete when all of its linked Subtasks
are verified and marked `[x]`.

---

## 4. Quality, Design, & Maintainability

### Standard Architectural Practices
- **Design Patterns:** Adhere to strictly typed, modern design patterns
  appropriate for the project's language and chosen type checker (see
  `../../STACK.md`).
- **Observability First:** Ensure proper logging, telemetry, and transaction
  tracing are integrated natively into all new features (see
  `../../references/logs/`).
- **Testing Requirements:** Write tests concurrently with feature development.
  Cover **both tracks**: unit tests AND real system interaction / feature &
  workflow tests, including edge cases (see `../../references/tests/`).
- **Containerization:** Utilize Docker (or the project's equivalent) to spin up
  isolated environments for databases, testing, or service dependencies. Apply
  the deployment optimization checklist (see
  `../../references/deployment/optimization_checklist.md`).
- **Dependency Management:** Always verify the project's dependency files when
  adding new packages. Ensure they are assigned to the correct deployment or
  development groups (see `../../references/tooling/`).

### Clarity & Collaboration
- **Stop and Ask:** If a requirement, contract, or design decision is ambiguous,
  presents an architectural flaw, or lacks logical sense, **stop and ask for
  clarification** before making assumptions (see `../user/user_input.md`).
- **Documentation Updates:** If you introduce new configurations, architectural
  improvements, or setup flags, immediately **update the project-level README**
  and the relevant `references/` files.
- **Environment Variables:** If valid environment variables, API keys, or custom
  `.env` parameters are required to test or execute any module, explicitly
  prompt for these values.

### Documentation & Version Verification
- **Verify Before Coding:** Before modifying or adding code, consult the
  official online documentation of the respective library to ensure
  implementation aligns with modern best practices (see `../planning/planning.md`).
- **Strict Versioning:** Always cross-reference your implementation with the
  exact library versions defined in the project's dependency manager to prevent
  deprecation errors or version conflicts.
- **Version & Link:** Bump the `version` in the task's frontmatter and update
  its changelog and `links` (including links to older tasks/features superseded).

---

## 5. Fallback Policy (Adhere to `../quality/fallback_policy.md`)

- **No blanket `try/except`.** Handle errors where they occur, with intent.
- **No hard pre-created fallbacks.** Fallbacks are created **only** when the
  user explicitly requests them.
- Surface errored states honestly (logged, traced, reported) — never silently
  masked.
