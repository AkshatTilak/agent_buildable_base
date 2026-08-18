---
version: 1.0.0
updated: 2026-08-18
id: setup_ci
links:
  - ../../STACK.md
  - ../../references/tooling/tooling.md
  - ../../references/tests/tests.md
  - ../../references/deployment/optimization_checklist.md
  - ../planning/init_project.md
---

# Setup CI — GitHub Actions

> **Purpose**: Set up GitHub Actions for the project where possible. Detect the
> stack and generate workflows for lint, typecheck, test, build, and optional
> deploy. This is also an improvement/verification item.

## 1. When To Use

- During project init (see `../planning/init_project.md`).
- When CI is missing or needs improvement.
- As a verification item in `../execution/work_verification.md`.

## 2. Process

1. **Detect** the stack and tooling from `../../STACK.md`.
2. **Generate** `.github/workflows/ci.yml` (or equivalent) with jobs for:
   - **Lint** (the recorded linter).
   - **Typecheck** (the recorded type-checker).
   - **Test** (both tracks: unit + system/feature/workflow — see
     `../../references/tests/`).
   - **Build** (with deployment optimization — see
     `../../references/deployment/optimization_checklist.md`).
   - **Deploy** (optional, only if the user wants it).
3. **Respect OS/shell**: use the correct runner and shell for the project.
4. **Verify** the workflows are valid and runnable.

## 3. Output

- `.github/workflows/` files.
- Updated `../../STACK.md` CI section.
- Note in the project changelog.
