---
version: 1.0.0
updated: 2026-08-18
id: conventions
links:
  - CODING_PHILOSOPHY.md
  - workflows/quality/fallback_policy.md
  - tasks/tasks.md
---

# CONVENTIONS — Naming, Zero-State & Duplicate Handling

> **Purpose**: Hard conventions that apply to every file, folder, identifier,
> and structure in this workspace. These exist to prevent duplicates,
> ambiguity, and drift. They are **mandatory**, not advisory.

## 1. Casing & Naming (snake_case)

- **All** files, folders, and identifiers use **snake_case** (`lowercase_with_underscores`).
- **No** camelCase, PascalCase, kebab-case, or mixed-case for files/folders/identifiers.
- **Numeric prefixes** are allowed **only** in `tasks/sub/` to express ordering
  (e.g. `01_database_setup.md`). They are not used elsewhere.
- Acronyms are lowercased in snake_case (`http_client.py`, not `HTTP_client.py`).
- A single canonical name exists for every concept. See §3.

## 2. Zero-State Convention

Every structure (feature, task, reference, design, module, UI component, data
store) MUST explicitly define its states:

1. **Empty / zero state** — what it looks like when newly initialized or empty.
2. **Populated state** — what it looks like with real value/content.
3. **Errored state** — what it looks like when something fails.

> **Fallback is NOT a default.** A fallback behavior is only added if the user
> explicitly requests one (see `workflows/quality/fallback_policy.md`). Otherwise the
> errored state is surfaced honestly (logged, traced, reported) — never silently
> masked by a hard-coded fallback.

## 3. Duplicate-Name Root-Causing Rule

- **No** `if` / `or` / `and` conditions with similar-but-duplicate names.
- When two or more names refer to the same concept, **fix the root source** and
  unify them into **one canonical name** — do not add a third alias.
- Before introducing a new name, search the workspace for existing names that
  already cover the concept (see `workflows/quality/recheck_codebase.md`).
- If a duplicate is found, the root cause (where the divergent names originated)
  must be corrected, and the canonical name documented in the relevant
  `references/` file.

## 4. Fallback Policy (pointer)

- No blanket `try/except`.
- No hard pre-created fallbacks.
- Fallbacks are created **only** when the user requests them.
- Full policy: `workflows/quality/fallback_policy.md`.

## 5. Versioning (pointer)

- Every file carries a YAML frontmatter header with `version`, `updated`, `id`,
  and `links`.
- The base version lives in `VERSION` and `CHANGELOG.md`.
- See `tasks/tasks.md` and `references/references.md` for per-file conventions.

## 6. Enforcement

- `workflows/execution/work_verification.md` audits for casing violations, duplicate names,
  and missing zero-state definitions.
- `workflows/quality/recheck_codebase.md` catches drift when workers forget to follow these.
