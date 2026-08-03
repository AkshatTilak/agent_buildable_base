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

6. **Documentation Integrity**:
   - Complete audit and update of `agent_buildable_base/references/` to accurately document v7 patterns.

## Acceptance Criteria
All 6 Base Tasks and 16 Subtasks marked `[x]` with full automated test execution passing.
