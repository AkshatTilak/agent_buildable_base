---
version: 1.0.0
updated: 2026-08-18
id: user_preferences
links:
  - STACK.md
  - workflows/router.md
  - workflows/user/user_input.md
---

# USER_PREFERENCES — Per-User Preferences

> **Purpose**: Records **user-level** preferences (not project-level). These are
> the defaults the user prefers across all projects. They can be **overridden
> per-project** in `STACK.md`.
>
> Every workflow should consult this file (via `workflows/router.md`) before
> executing, so behavior matches the user's preferences.

## 1. Tooling Preferences

| Concern | Preferred tool | Notes |
|---------|----------------|-------|
| Linter | _e.g. ruff, eslint_ | |
| Formatter | _e.g. black, prettier_ | |
| Type checker | _e.g. mypy, pyright, tsc_ | |
| Dependency manager | _e.g. uv, poetry, npm, pnpm_ | |
| Test runner | _e.g. pytest, vitest_ | |

> These are **defaults**. The user may choose different tools per project; that
> choice is recorded in `STACK.md` and takes precedence.

## 2. Casing & Naming

- Default casing: **snake_case** (per `CONVENTIONS.md`).
- If the user prefers a different casing for a specific project, record it in
  `STACK.md`.

## 3. Fallback Stance

- Default: **strict** — no blanket `try/except`, no hard pre-created fallbacks
  (per `workflows/quality/fallback_policy.md`).
- If the user prefers a more permissive stance, record it here.

## 4. Verbosity

- Default: **concise** — keep answers short and impersonal.
- Options: concise | balanced | detailed.

## 5. Other Preferences

- _Add any other user-level preferences here (e.g. preferred commit style,
  preferred documentation depth, preferred testing rigor)._

## 6. How To Update

- Update this file when the user expresses a new preference.
- Bump the `version` in the frontmatter and note the change in the changelog.
- Project-specific overrides go in `STACK.md`, not here.
