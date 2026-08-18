---
version: 1.0.0
updated: 2026-08-18
id: logs
links:
  - ../../CODING_PHILOSOPHY.md
  - ../../workflows/execution/work_verification.md
  - ../tests/
---

# Logs — Logging, Tracing & Observability

> **Purpose**: Defines how logs and traces are produced, stored, and found. All
> log types — docker, dev, failure, and test — must be handled properly and be
> easy to find and maintain.

## 1. Log Types & Locations

| Log type | Where it lives | Notes |
|----------|----------------|-------|
| **Dev logs** | `logs/dev/` | Application logs during development. |
| **Docker logs** | `logs/docker/` | Container output; also available via `docker logs`. |
| **Failure logs** | `logs/failure/` | Captured errors, exceptions, and crash traces. |
| **Test logs** | `logs/test/` | Test runner output and failures. |

> The `logs/` directory is gitignored (see `.gitignore`). Log locations are
> documented here so they are easy to find even though they are not committed.

## 2. Format

- Logs are **structured** (key/value or JSON) where the stack supports it.
- Every log line includes a timestamp, level, and a correlation/trace id where
  applicable.
- **Tracing**: transaction and request tracing is native to features (see
  `../../CODING_PHILOSOPHY.md` §4).

## 3. Warning / Bloat Cleanup Policy

- Warnings and noisy logs **bloat runs**. Keep runs clean.
- **Do not** leave noisy warnings in place. Either:
  - fix the root cause, or
  - downgrade to debug level, or
  - log them to a dedicated location so they don't pollute normal output.
- `../../workflows/execution/work_verification.md` audits for log bloat and improper log
  formats.

## 4. Retention & Maintenance

- Define a retention policy per log type (e.g. dev logs rotated daily, failure
  logs kept longer).
- Logs must be easy to find: name files clearly and keep the index in this file
  up to date.

## 5. Index

> Add log locations here as they are created.

| Log type | Path | Retention |
|----------|------|-----------|
| Dev | `logs/dev/` | _TBD_ |
| Docker | `logs/docker/` | _TBD_ |
| Failure | `logs/failure/` | _TBD_ |
| Test | `logs/test/` | _TBD_ |
