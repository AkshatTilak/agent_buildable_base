---
version: 1.0.0
updated: 2026-08-18
id: db
links:
  - ../../tasks/tasks.md
  - ../../workflows/execution/work_verification.md
  - ../structure/
---

# DB — Database, Models & Migrations

> **Purpose**: Keeps the database schema, models, and migrations **always in
> sync**, and provides simple human-readable files to easily see models and
> schemas.

## 1. The Sync Rule

- The **schema**, the **models**, and the **migrations** must never drift apart.
- Any change to one MUST be reflected in the other two in the same task.
- `../../workflows/execution/work_verification.md` audits that schema, models, and
  migrations are in sync before a task is marked `[x]`.

## 2. Simple View Files

- Keep simple, human-readable files that show the current models/schemas at a
  glance:
  - `references/db/models.md` — a readable list of all models and their fields.
  - `references/db/schema.md` — a readable view of the database schema.
- These are **views**, not the source of truth. The source of truth is the
  actual schema/migration files in the codebase.

## 3. Migration Tracking

- Every migration is recorded with:
  - a unique id/name,
  - the date,
  - what it changes,
  - whether it is backward compatible (a user choice — see
    `../../workflows/user/user_input.md`).
- Keep a migration log in `references/db/migrations.md`.

## 4. Backward Compatibility

- Prefer backward-compatible migrations, but **ask the user** whether to preserve
  backward compatibility or break it. Never assume silently.

## 5. Index

> Add model/schema/migration files here as they are created.

| File | Purpose | Status |
|------|---------|--------|
| `models.md` | Human-readable model list | _empty_ |
| `schema.md` | Human-readable schema view | _empty_ |
| `migrations.md` | Migration log | _empty_ |
