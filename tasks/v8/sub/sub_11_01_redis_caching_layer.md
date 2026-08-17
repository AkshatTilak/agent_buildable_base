# Subtask 11_01: Redis Caching Layer

## Parent Base Task
`tasks/v8/base/11_backend_performance_optimizations.md`

## Objective
Add Redis caching for hub resolution, user session data, model registry, and collection metadata.

## Tasks
1. [x] Cache hub metadata by ID and slug (frequently called, rarely changed).
2. [x] Cache JWT-decoded user info to avoid repeated DB lookups.
3. [x] Cache available models list (model registry).
4. [x] Cache Qdrant collection schemas (collection metadata).

## Definition of Done
- Redis caching layer in place for hub, session, model registry, and collection metadata on actual Redis (`:6379`).
- Cache hit/miss ratios visible in gateway logs.
- If errors arise, inspect Docker container logs (`docker compose logs redis db`) and fix root causes directly.
- Deleting test data is not necessary.
