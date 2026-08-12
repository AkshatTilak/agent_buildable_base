# Goal: Version 7 - Platform Stabilization, Lifecycle Management & Legacy Cleanup

## Objective
Execute a comprehensive v7 platform stabilization cycle across the monorepo to fix flaws introduced in v6, eliminate legacy debt, complete the user authentication lifecycle (soft/hard deletion, session revocation, environment super admin), harden Gateway APIs (fail-fast, no silent fallbacks, accurate health checks), align Docker persistent storage with local host paths, deliver clean startup UI empty-states, expand unit/integration test coverage across all Gateway endpoints, and overhaul system reference documentation.

## Key Outcomes
1. **User Lifecycle & Auth**:
   - Database soft deletion schema (`is_deleted`, `deleted_at`).
   - User self-initiated soft delete (`DELETE /auth/me` or `/users/me`) and admin hard purge.
   - Secure logout endpoint (`POST /auth/logout`) with session deletion and token invalidation.
   - Bootstrapping of super admin and test users directly from `.env` (removing the first-registered user admin hack).
   - Auth UI overhaul with dedicated screens for suspended, soft-deleted, or disabled accounts.

2. **Gateway Hardening & Visibility**:
   - Fix `/health` endpoint to accurately compute `degraded` and `unhealthy` states rather than hardcoding `200 OK healthy`.
   - Remove silent fallbacks across Gateway endpoints and frontend App shell.
   - Replace HTML error pages in `proxy.py` with structured JSON error responses.
   - Full stack trace visibility in `gateway.log` for unhandled exceptions.

3. **UI Polish & Legacy Code Removal**:
   - Clean initial startup with zero seeded mock data.
   - Polished empty states across Hub Directory, Ingestion, Agent, Workflow, and Eval workspaces.
   - Enhanced `InfrastructurePage.tsx` with dependency health checks, launch commands, and redirect links for non-core UIs.
   - Pruning of deprecated v5 components, dead imports, and redundant auth utility functions.

4. **Infrastructure & Storage**:
   - Update `docker-compose.yml` to bind database/store volumes to `./data/*` local host paths.

5. **API Test Coverage**:
   - Comprehensive test suite covering all Gateway routes (Auth, Users, Hubs, Ingestion, Agents, Workflows, Evals, MCP, Proxy) without building heavy containers.

6. **Workflow Canvas UX Fixes**:
   - Starter template defaults (RAG / classifier / multi-agent) seed the canvas instead of opening empty.
   - Node positions persist across reloads via frontend-only localStorage drafts, pushed to the backend draft endpoint on explicit Save.
   - Newly added nodes never overlap existing nodes.
   - Agent / retrieval property dropdowns source resources from linked hubs.
   - Links panel loads without a 404 (incoming links read via `GET /links?direction=incoming`).

7. **Workflow Canvas Interactivity & Hub Panel Fixes**:
   - Property-drawer dropdowns show loading / empty / error states.
   - Canvas supports pan, zoom, smooth node movement, and keyboard shortcuts (delete, duplicate, save, undo/redo, zoom).
   - Canvas can enter and exit fullscreen.
   - Members panel is null-safe and renders without crashing when `email` / `display_name` are null.
   - Links panel shows target hub name/type/slug (backend denormalization + frontend fallback) instead of empty columns.

8. **Documentation Integrity**:
   - Complete audit and update of `agent_buildable_base/references/` to accurately document v7 patterns.

## Acceptance Criteria
All 8 Base Task groups and 44 Subtasks marked `[x]` with full automated test execution passing.
