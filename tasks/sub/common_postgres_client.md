# Subtask: Common PostgreSQL Client

**Parent Link:** `base/common_library.md`

## Actionable Steps
- [x] Switch from synchronous `create_engine()` to `create_async_engine()` with `asyncpg`.
- [x] Use `AsyncSession` throughout.
- [x] Setup connection pooling.
- [x] Provide `get_async_db()` FastAPI dependency.

## Definition of Done
- Async PostgreSQL queries execute successfully using the unified connection pool.
