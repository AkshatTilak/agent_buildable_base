---
version: 1.0.0
updated: 2026-08-18
id: extend_task
links:
  - ../../tasks/tasks.md
  - ../../tasks/_templates/sub.md
  - ../../features/features.md
  - ../user/user_input.md
---

# Extend Task — Deepen & Expand A Task

> **Purpose**: One-shotting a task is impossible — tasks and subtasks often lack
> depth. This workflow deepens a base/sub task iteratively by asking the user
> questions and offering openings and directions, then expanding in more and
> more detail.

## 1. When To Use

- When a task is defined but lacks actionable depth.
- When a task needs more steps, edge cases, or dependencies.
- When a task's Definition of Done is too vague to verify.

## 2. Process

1. **Review** the current task (`tasks/base/` or `tasks/sub/`).
2. **Ask the user** targeted questions to surface missing depth (see
   `../user/user_input.md`).
3. **Offer openings and directions** the user can choose from.
4. **Expand** the task with more detailed, ordered actionable steps, edge cases,
   dependencies, and a precise Definition of Done.
5. **Link** the task to the features/designs/references it touches, and to any
   older tasks it supersedes.

## 3. Depth Checklist

- Are the actionable steps specific and ordered?
- Are edge cases listed and testable?
- Are dependencies (packages, env vars, other tasks) complete?
- Is the Definition of Done verifiable?
- Are both test tracks (unit + system/feature/workflow) covered?

## 4. Output

- Updated task file with expanded detail.
- Updated frontmatter (`version`, `links`, `depends_on`).
- Cross-links to `features/`, `design/`, and `references/`.
