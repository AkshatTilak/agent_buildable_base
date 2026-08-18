---
version: 1.0.0
updated: 2026-08-18
id: planning
links:
  - ../../design/design.md
  - ../../features/features.md
  - ../../tasks/tasks.md
  - ../user/user_input.md
---

# Planning — Research, Options & Recommendations

> **Purpose**: Before any implementation, plan. Research the options, present
> recommendations, and let the user choose. Planning is as important as coding.

## 1. When To Plan

- Before starting a new feature, workflow, or significant task.
- Before choosing a library, tool, or architecture.
- Before any change that affects backward compatibility.

## 2. Research First

- **Web research**: consult official documentation and reputable sources.
- **Playwright / browser**: verify behavior against real pages/APIs where
  relevant.
- **Documentation**: read the official docs of the libraries/tools involved.
- **Workspace search**: check `references/` and existing code for prior art
  before proposing anything new.

## 3. Present Options & Recommendations

- Present **multiple implementation options** with trade-offs.
- Give a **recommendation** with rationale.
- Ask the user to choose (see `../user/user_input.md`).

## 4. Backward Compatibility Is A Choice

- Prefer backward-compatible changes, but **ask the user** whether to preserve
  backward compatibility or break it. Never assume silently.

## 5. Output

- Record the chosen option and rationale in the relevant `design/` or
  `features/` file.
- Link the decision to the tasks that implement it.
