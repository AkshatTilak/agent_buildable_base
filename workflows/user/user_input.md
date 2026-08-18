---
version: 1.0.0
updated: 2026-08-18
id: user_input
links:
  - ../../design/design.md
  - ../../features/features.md
  - ../../tasks/tasks.md
  - ../../USER_PREFERENCES.md
  - ../planning/planning.md
  - ../planning/init_project.md
---

# User Input — Conditioning Flows, Features & Exceptions

> **Purpose**: User input is essential to conditioning and thinking through any
> system flow, feature, handling, or exception. **Always ask the user.** Never
> silently assume.

## 1. When To Ask

- When defining a new flow, feature, or workflow.
- When handling exceptions or edge cases.
- When choosing tooling (linter, formatter, type-checker, dependency manager).
- When choosing OS/shell assumptions.
- When a change affects backward compatibility.
- When a requirement is ambiguous (see `../execution/work_principle.md`
  "Stop and Ask").

## 2. What To Ask

- **Flows**: how should this flow behave? What are the steps and decision points?
- **Features**: what should this feature do? What are its boundaries?
- **Exceptions**: how should errors be handled? Should a fallback exist?
- **Tooling**: any preferences for linter/formatter/type-checker/dependency manager?
- **Environment**: which OS and shell are you on?
- **Backward compatibility**: preserve it or break it?

## 3. How To Ask

- Ask **targeted, specific questions** — not open-ended ones.
- Offer **options** where possible, with a recommendation.
- Record the user's answers in the relevant `design/`, `features/`, or `tasks/`
  file so the decision is traceable.
- Update `../../USER_PREFERENCES.md` when the user expresses a durable
  preference.

## 4. Fallback Decisions

- Fallbacks are created **only** when the user requests them (see
  `../quality/fallback_policy.md`). Ask explicitly before adding any fallback.
