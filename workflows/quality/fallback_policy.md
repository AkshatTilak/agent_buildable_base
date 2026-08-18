---
version: 1.0.0
updated: 2026-08-18
id: fallback_policy
links:
  - ../../CONVENTIONS.md
  - ../../CODING_PHILOSOPHY.md
  - ../execution/work_principle.md
  - ../user/user_input.md
---

# Fallback Policy — When & How To Fallback

> **Purpose**: Define when and how fallbacks are used. The default is **no
> fallback**. Fallbacks are created **only** when the user explicitly requests
> them.

## 1. The Default: No Fallback

- **No blanket `try/except`** that swallows errors.
- **No hard pre-created fallbacks** baked into the system by default.
- Errors are surfaced honestly: logged, traced, and reported (see
  `../../references/logs/`).

## 2. When A Fallback Is Allowed

A fallback is allowed **only** when:
1. The user explicitly requests one, AND
2. The fallback is designed and documented (not a silent catch-all).

## 3. How To Implement A Fallback (When Requested)

- Design the fallback explicitly: what triggers it, what it does, what it logs.
- Document it in the relevant `features/` or `design/` file.
- Test the fallback path (see `../../references/tests/`).
- Record the user's request in the task/feature changelog.

## 4. Zero-State & Errored State

- Every structure defines its empty/zero, populated, and errored states (see
  `../../CONVENTIONS.md` §2).
- The errored state is surfaced honestly. A fallback is an **additional**,
  user-requested behavior — not a replacement for honest error reporting.
