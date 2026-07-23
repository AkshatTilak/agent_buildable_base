# B5-03: User Authentication & RBAC

> **Status:** `[x]`  
> **Owner:** `gateway` (new `auth/` package)  
> **Secondary:** `common/models`, `frontend`, `infrastructure`  
> **Complexity:** 🔴 High (6 subtasks)

---

## Objective

Add opt-in user authentication via Google and GitHub OAuth2. Issue JWTs for session management. Enforce role-based access control (admin / editor / viewer) across all dashboard routes and API endpoints. Auth remains disabled by default (`AUTH_ENABLED=false`) for backward compatibility.

---

## Acceptance Criteria

- [x] `User` and `UserSession` DB models created with Alembic migration
- [x] Google OAuth login/callback flow works end-to-end (redirect → callback → JWT issued)
- [x] GitHub OAuth login/callback flow works end-to-end
- [x] JWT middleware validates tokens on protected routes
- [x] `require_role()` dependency enforces admin/editor/viewer permissions
- [x] When `AUTH_ENABLED=false`, all routes remain open (no regression)
- [x] Frontend has login page with Google/GitHub buttons
- [x] Auth state persisted in Zustand store; header shows user avatar + role
- [x] Protected routes redirect unauthenticated users to `/login`
- [x] Admin panel for user management (list users, change roles)

---

## Roles & Permissions Matrix

| Resource | Admin | Editor | Viewer |
|---|---|---|---|
| Dashboard / System Metrics | ✅ | ✅ | ✅ |
| View Agents / Workflows / Evals | ✅ | ✅ | ✅ |
| Create/Edit Agents | ✅ | ✅ | ❌ |
| Create/Edit Workflows | ✅ | ✅ | ❌ |
| Run Evaluations | ✅ | ✅ | ❌ |
| Use Playground | ✅ | ✅ | ❌ |
| Manage MCP Servers | ✅ | ✅ | ❌ |
| Settings / API Keys | ✅ | ❌ | ❌ |
| User Management | ✅ | ❌ | ❌ |

---

## Linked Subtasks

| ID | Title | File |
|---|---|---|
| S5-03a | Auth Package Scaffold & DB Models | [`S5-03a.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-03a.md) |
| S5-03b | Google OAuth Provider | [`S5-03b.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-03b.md) |
| S5-03c | GitHub OAuth Provider | [`S5-03c.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-03c.md) |
| S5-03d | JWT Middleware & Role-Based Dependencies | [`S5-03d.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-03d.md) |
| S5-03e | Frontend: Login Page & Auth State | [`S5-03e.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-03e.md) |
| S5-03f | Frontend: Route Guards, User Badge, Admin Panel | [`S5-03f.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-03f.md) |
