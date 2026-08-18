---
version: 1.0.0
updated: 2026-08-18
id: tooling
links:
  - ../../STACK.md
  - ../../workflows/planning/init_project.md
  - ../../workflows/ci/setup_ci.md
---

# Tooling — Linters, Formatters, Type Checkers, Dependency Managers, OS & Shell

> **Purpose**: Records the chosen linter, formatter, type-checker, and dependency
> manager, plus OS/shell-specific command variants. This keeps every generated
> command and script correct for the real environment.

## 1. Tooling Record

The authoritative record of chosen tools lives in `../../STACK.md`. This file
documents **how** to use them and the OS/shell variants.

| Concern | Tool | Config location | Invocation |
|---------|------|-----------------|------------|
| Linter | _from STACK.md_ | _from STACK.md_ | `...` |
| Formatter | _from STACK.md_ | _from STACK.md_ | `...` |
| Type checker | _from STACK.md_ | _from STACK.md_ | `...` |
| Dependency manager | _from STACK.md_ | _from STACK.md_ | `...` |

> Tooling choices are **asked to the user** at init (see
> `../../workflows/user/user_input.md` and `../../workflows/planning/init_project.md`). Do not
> silently pick a tool.

## 2. OS & Shell Awareness

- The OS (Windows/Linux/macOS) and shell (powershell/bash/cmd/zsh) are recorded
  in `../../STACK.md`.
- **Commands, scripts, and paths MUST be generated for the recorded OS + shell.**
- Common variants:
  - **PowerShell**: use `;` to chain commands (never `&&`); paths use `\`.
  - **bash/zsh**: use `&&`; paths use `/`.
  - **cmd**: use `&`/`&&`; paths use `\`.
- Never assume bash on Windows.

## 3. Dependency Management

- Use the project's chosen dependency manager (see `../../STACK.md`).
- Lock files are committed for reproducibility where the tool supports it.
- Keep dependency groups clean (dev vs prod).

## 4. CI Integration

- The same tooling is wired into CI via `../../workflows/ci/setup_ci.md`.

## 5. Index

> Add tooling-specific notes here as they are created.

| Tool | Notes | Status |
|------|-------|--------|
| _(none yet)_ | | |
