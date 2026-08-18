---
version: 1.0.0
updated: 2026-08-18
id: extend_goal
links:
  - ../../tasks/tasks.md
  - ../../design/design.md
  - ../../features/features.md
  - ../user/user_input.md
---

# Extend Goal — Deepen & Expand A Goal

> **Purpose**: One-shotting a goal is impossible — tasks and subtasks often lack
> depth. This workflow expands a goal iteratively by asking the user questions
> and offering openings and directions they can choose, then expanding in more
> and more detail.

## 1. When To Use

- When a goal is defined but lacks depth.
- When a goal needs to grow to cover more of the system.
- When a goal's tasks/subtasks are too shallow to execute well.

## 2. Process

1. **Review** the current goal (`tasks/goal/`) and its Base Tasks.
2. **Ask the user** targeted questions to surface missing depth (see
   `../user/user_input.md`).
3. **Offer openings and directions** the user can choose from (e.g. "expand
   auth to include SSO?", "add observability to the pipeline?", "support
   multi-tenancy?").
4. **Expand** the goal and its Base Tasks/Subtasks in more and more detail,
   iterating until the user is satisfied.
5. **Link** the expanded goal to the features/designs it now covers.

## 3. Depth Checklist

- Are all major subsystems covered?
- Are edge cases and exception paths considered?
- Are non-functional requirements (performance, security, observability)
  addressed?
- Are user decisions recorded?

## 4. Output

- Updated `tasks/goal/goal.md` with expanded scope.
- New/updated Base Tasks and Subtasks.
- Cross-links to `features/` and `design/`.
