# Subtask 11_01: Redis Caching Layer

## Parent Base Task
`tasks/v8/base/11_backend_performance_optimizations.md`

## Objective
Add Redis caching for hub resolution, user session data, model registry, and collection metadata.

## Tasks
1. [ ] Cache hub metadata by ID and slug (frequently called, rarely changed).
2. [ ] Cache JWT-decoded user info to avoid repeated DB lookups.
3. [ ] Cache available models list (model registry).
4. [ ] Cache Qdrant collection schemas (collection metadata).

## Definition of Done
- Redis caching layer in place for hub, session, model registry, and collection metadata.
- Cache hit/miss ratios visible in gateway logs.
