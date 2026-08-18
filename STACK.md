---
version: 1.0.0
updated: 2026-08-18
id: stack
links:
  - references/tooling/
  - workflows/planning/init_project.md
  - USER_PREFERENCES.md
---

# STACK — Project Stack, Tooling & Environment Record

> **Purpose**: This file is the single source of truth for the stack, tooling,
> OS, and shell of the **current project**. It is created as a template here in
> the base and **filled in by `workflows/planning/init_project.md`** when a project is
> bootstrapped or an existing codebase is onboarded.
>
> Every agent MUST consult this file before generating commands, paths, or
> scripts so that everything matches the real environment.

## 1. Stack

| Field | Value |
|-------|-------|
| Primary language(s) | _e.g. Python 3.12_ |
| Framework(s) | _e.g. FastAPI, React_ |
| Runtime / version manager | _e.g. uv, nvm, asdf_ |
| Package / dependency manager | _e.g. uv, poetry, npm, pnpm_ |
| Container runtime | _e.g. Docker, Podman_ |
| Database(s) | _e.g. PostgreSQL 16_ |
| Message broker(s) | _e.g. Redis, RabbitMQ_ |

## 2. Tooling

| Concern | Tool | Config location | Invocation |
|---------|------|-----------------|------------|
| Linter | _e.g. ruff, eslint_ | _e.g. pyproject.toml_ | `ruff check .` |
| Formatter | _e.g. black, prettier_ | _e.g. pyproject.toml_ | `black .` |
| Type checker | _e.g. mypy, pyright, tsc_ | _e.g. pyproject.toml_ | `mypy src/` |
| Test runner | _e.g. pytest, vitest_ | _e.g. pyproject.toml_ | `pytest` |
| Coverage | _e.g. coverage.py, c8_ | _e.g. pyproject.toml_ | `coverage run -m pytest` |

> Tooling choices are **asked to the user** at init (see `workflows/user/user_input.md`
> and `workflows/planning/init_project.md`). Do not silently pick a tool.
> User-level defaults live in `USER_PREFERENCES.md`; project overrides go here.

## 3. Environment

| Field | Value |
|-------|-------|
| Operating system | _e.g. Windows 11, Ubuntu 22.04, macOS 14_ |
| Shell | _e.g. powershell, bash, cmd, zsh_ |
| Shell-specific notes | _e.g. use `;` not `&&` in PowerShell; paths use `\`_ |

> **OS + shell awareness is mandatory.** Commands, scripts, and paths MUST be
> generated for the OS and shell recorded here. Never assume bash on Windows.

## 4. CI / CD

| Field | Value |
|-------|-------|
| CI provider | _e.g. GitHub Actions_ |
| Workflow files | _e.g. `.github/workflows/ci.yml`_ |
| Triggered on | _e.g. push, pull_request_ |

> See `workflows/ci/setup_ci.md` for generating CI workflows.

## 5. How to update this file

- Update this file whenever the stack, tooling, OS, or shell changes.
- Bump the `version` in the frontmatter and add a note to the project changelog.
- Keep it in sync with `references/tooling/` and `.gitignore` per-stack blocks.
