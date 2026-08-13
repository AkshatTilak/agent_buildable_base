# Subtask 11_03: Response Optimization

## Parent Base Task
`tasks/v8/base/11_backend_performance_optimizations.md`

## Objective
Add DB indexes, cursor pagination, and eager loading for related entities.

## Tasks
1. [ ] Add proper DB indexes for frequently filtered columns (hub_id, user_id, status, created_at).
2. [ ] Implement cursor-based pagination where offset pagination is used.
3. [ ] Add `select_in_loading` / `joinedload` for related entities.

## Definition of Done
- DB indexes added for frequently filtered columns.
- Cursor pagination implemented where applicable.
- Eager loading used for related entities.
