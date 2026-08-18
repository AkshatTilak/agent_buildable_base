# Changelog

All notable changes to the **agent_buildable_base** scaffold are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

The current version is tracked in [`VERSION`](VERSION) and mirrored as git tags
(`v1.0.0`, `v1.1.0`, ...).

## [Unreleased]

## [1.1.0] - 2026-08-18

### Added
- **`workflows/router.md`**: routes user prompts to the best workflow. Models
  check this FIRST before executing any workflow.
- **`USER_PREFERENCES.md`**: per-user preferences (tools, casing, fallback
  stance, verbosity), overridable per-project in `STACK.md`.
- **`skills/manage_skills.md`**: a skill for creating/updating/deleting skills
  (CRUD on the skills library).
- **Skill hub integration** in `workflows/user/find_skills.md`: discovers skills
  from agentskillshub.dev and aihero.dev and imports them into our format.

### Changed
- **`prompts/` → `workflows/`**: renamed and reorganized into subfolders by
  category — `execution/`, `planning/`, `quality/`, `user/`, `ci/`.
- **`agent.md`**: now references `workflows/router.md` as the routing entry
  point and `USER_PREFERENCES.md` for user preferences.
- **`skills/` restructured**: from flat files to folder-per-skill with
  `SKILL.md` + optional `scripts/`, grouped by domain (`qa/`, `ui/`, `debug/`).
- **`README.md`**: updated tree to show `workflows/` and `USER_PREFERENCES.md`.

### Removed
- **`prompts/`** folder (all files moved to `workflows/`).

## [1.0.0] - 2026-08-18

### Added
- **Versioning everywhere**: root `VERSION` file, this `CHANGELOG.md`, per-file
  YAML frontmatter (`version`, `updated`, `id`, `links`), and git tag convention.
- **`STACK.md`**: per-project record of detected/chosen stack, tooling
  (linter, formatter, type-checker, dependency manager), OS, and shell.
- **`CONVENTIONS.md`**: enforced snake_case, duplicate-name root-causing rule,
  zero-state convention, and fallback policy pointer.
- **`CODING_PHILOSOPHY.md`**: stack-agnostic coding philosophy.
- **`design/`** tree: system, workflows, and UX design kept separate from tasks.
  Workflows are designed **before** tasks are created.
- **`features/`** tree: feature specs with mermaid diagrams, full connections
  and paths, caveats, and a per-feature changelog.
- **`references/tests/`**, **`references/logs/`**, **`references/db/`**,
  **`references/tooling/`**: two-track testing, log/tracing management,
  schema↔migration sync, and tooling/OS/shell conventions.
- **`references/deployment/`** optimization checklist: multi-stage builds,
  cache cleanup after install, minimal base images, image-size verification.
- **`tasks/_templates/`**: reusable Goal/Base/Sub/Temp skeletons with
  frontmatter, IDs, dependency graphs, and complexity rubric.
- **`skills/`** library (tracked): traceback_check, ui_ux_creation, qa_backend,
  qa_frontend, qa_docker, qa_network.
- **`assets/`** folder (gitignored): user-provided test inputs (pdf, video,
  audio, scripts, docs).
- **New prompts**: `planning.md`, `extend_goal.md`, `extend_task.md`,
  `recheck_codebase.md`, `user_input.md`, `fallback_policy.md`,
  `init_project.md`, `find_skills.md`, `setup_ci.md`.
- **Backward-compatibility as a user choice** during planning and design.
- **Cross-linking convention**: tasks link to features/design/references and to
  older tasks/versions they supersede.

### Changed
- **Stack-agnostic**: removed the Python/Docker bias. Stack, tooling, OS, and
  shell are detected or asked at init time.
- **`.gitignore`**: rewritten as a stack-agnostic base; per-stack blocks are
  appended at init.
- **`agent.md`**: Architect now runs the `init_project` entry point, detects
  stack/OS/shell/tooling, designs workflows first, and triggers CI setup.
- **`README.md`**: rewritten to document the full new tree and philosophy.
- **`references/references.md`**: removed orphan placeholder line; added
  `tests/`, `logs/`, `db/`, `tooling/` definitions and an index mechanism.
- **`tasks/tasks.md`**: added Goal template, task IDs, frontmatter, dependency
  graph, explicit ordering rules, and a complexity rubric.

### Fixed
- **LICENSE**: corrected typo "TIlak" → "Tilak".

[Unreleased]: https://example.com/agent_buildable_base/compare/v1.0.0...HEAD
[1.0.0]: https://example.com/agent_buildable_base/releases/tag/v1.0.0
