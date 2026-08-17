# Base Task 12: Frontend Performance Optimizations (v8)

## Objective
Optimize the React frontend for faster renders, fewer API calls, and better state management.

## Why
The frontend makes duplicate API fetches, duplicates state between Zustand and React Query, renders expensive components (workflow canvas, eval dashboards, member lists) without memoization, and loads heavy pages eagerly. This causes slow renders and excessive network traffic.

## Scope
- **API call deduplication** — Review `frontend/src/services/api.ts` for duplicate fetches:
  - Use React Query's built-in deduplication (`useQuery` with `staleTime`).
  - Add `staleTime` and `gcTime` to all query hooks.
  - Implement optimistic updates for hub/agent/workflow CRUD.
- **State management cleanup** — Review Zustand slices:
  - Dedupe state duplicated between Zustand and React Query cache.
  - Clear stale state on hub switch / logout.
  - Proper state reset on navigation away from hub context.
- **Component optimization**:
  - Memoize expensive components (workflow canvas, eval dashboards, member lists).
  - Virtual scroll for large lists (documents, chunks, eval results).
  - Lazy-load heavy pages (Playground, MCP Hub, Settings).
- **Bundle size** — Identify and tree-shake unused dependencies.
- **Request batching** — Batch related API calls on page load (e.g., hub metadata + members + links in one request, or parallel `Promise.all`).

## Associated Subtasks
1. `[x]` `sub_12_01_api_deduplication.md`: React Query staleTime/gcTime, optimistic updates, request batching.
2. `[x]` `sub_12_02_state_management_cleanup.md`: Zustand/React Query dedup, state reset on hub switch/logout.
3. `[x]` `sub_12_03_component_optimization.md`: Memoization, virtual scrolling, lazy loading.
4. `[x]` `sub_12_04_bundle_size.md`: Tree-shake unused dependencies.

## Definition of Done
- All query hooks use `staleTime`/`gcTime`; optimistic updates for CRUD.
- Zustand state deduplicated and reset on hub switch/logout.
- Expensive components memoized; large lists virtual-scrolled; heavy pages lazy-loaded.
- Unused dependencies tree-shaken; bundle size reduced.
- Related API calls batched on page load.
- Lighthouse / DevTools before/after metrics show improvement.
