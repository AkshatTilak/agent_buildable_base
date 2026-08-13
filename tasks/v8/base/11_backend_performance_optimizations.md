# Base Task 11: Backend Performance Optimizations (v8)

## Objective
Identify and fix performance bottlenecks in the gateway, common services, and submodule backends — caching, query deduplication, response optimization, and startup optimization.

## Why
The backend performs repeated DB lookups (N+1 patterns), lacks caching for frequently-read data (hub metadata, sessions, model registry, collection schemas), and uses offset pagination. These cause latency and load issues under real usage.

## Scope
- **Redis caching layer** — Add caching for:
  - Hub resolution (frequently called, rarely changed) — cache hub metadata by ID and slug.
  - User session data — cache JWT-decoded user info to avoid repeated DB lookups.
  - Model registry — cache available models list.
  - Collection metadata — cache Qdrant collection schemas.
- **Query deduplication** — Identify and fix N+1 query patterns:
  - Hub member loading (currently individual queries per member).
  - Agent/workflow listing with related data.
  - Eval results with test case details.
- **Response optimization**:
  - Add proper DB indexes for frequently filtered columns (hub_id, user_id, status, created_at).
  - Implement cursor-based pagination where offset pagination is used.
  - Add `select_in_loading` / `joinedload` for related entities.
- **Startup optimization**:
  - Lazy-load submodule routes (defer import until route is first hit).
  - Connection pool tuning (Postgres pool size, Qdrant connection limits).
- **Cache invalidation** — Proper cache clearing on mutations:
  - Hub update/delete → clear hub cache.
  - Member add/remove → clear membership cache.
  - Collection create/delete → clear collection cache.

## Associated Subtasks
1. `[ ]` `sub_11_01_redis_caching_layer.md`: Redis caching for hub, session, model registry, collection metadata.
2. `[ ]` `sub_11_02_query_deduplication.md`: Fix N+1 query patterns (hub members, agent/workflow listing, eval results).
3. `[ ]` `sub_11_03_response_optimization.md`: DB indexes, cursor pagination, eager loading.
4. `[ ]` `sub_11_04_startup_and_cache_invalidation.md`: Lazy route loading, pool tuning, cache invalidation on mutations.

## Definition of Done
- Redis caching layer in place with proper invalidation on mutations.
- N+1 query patterns eliminated.
- DB indexes added; cursor pagination where applicable; eager loading for related entities.
- Submodule routes lazy-loaded; connection pools tuned.
- Cache hit/miss ratios visible in gateway logs.
