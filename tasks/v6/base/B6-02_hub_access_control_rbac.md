# B6-02: Hub Access Control, Scoping & RBAC v2

> **Status:** `[x] Completed`  
> **Owner:** `gateway/auth`, `gateway/api`  
> **Secondary:** `common/services`, `tests`  
> **Complexity:** 🔴 High (5 subtasks)

---

## Objective

Replace the flat V5 `admin/editor/viewer` global RBAC with the two-axis V6 model: a platform role
(`admin` / `member`) plus a per-hub role (`owner` / `maintainer` / `contributor` / `viewer`). Ship the
`HubContext` dependency that resolves and authorises `{hub_id}` on every nested route, the
`hub_resolver` service that validates cross-hub references, the hub/member/link management API, and the
isolation guard tests that make tenancy violations impossible to merge.

---

## Acceptance Criteria

- [x] `users.role` is replaced by `users.platform_role` (`admin` | `member`); V5 values are mapped `admin→admin`, `editor→member`, `viewer→member`.
- [x] `gateway/auth/hub_context.py` exposes `HubContext` and the `require_hub(hub_type=..., min_role=...)` dependency factory.
- [x] Failure semantics match `hubs.md` §5.2 exactly — non-members receive `404`, never `403`, so hub existence cannot be enumerated.
- [x] Platform `admin` short-circuits every hub check and is treated as `owner`.
- [x] Cross-hub references enforce the direction matrix and access levels (`read` vs `use`), and are non-transitive.
- [x] Hub management API (`gateway/api/hubs.py`) supports collection CRUD, membership CRUD, ownership transfer, and link management.
- [x] Last owner protection prevents a hub from becoming ownerless.
- [x] Mutating operations emit `audit_log` records with payload redaction.
- [x] Isolation test suite (`tests/test_hub_isolation.py`) and static AST guard (`tests/test_hub_query_guard.py`) pass cleanly.

---

## Subtasks

| Code | Title | Complexity | Owner | Status |
|---|---|---|---|---|
| [`S6-02a`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-02a.md) | Platform Role Refactor & Role Constants | 🟡 Med | `gateway/auth` | `[x]` |
| [`S6-02b`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-02b.md) | `HubContext` Dependency & `require_hub` Factory | 🔴 High | `gateway/auth` | `[x]` |
| [`S6-02c`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-02c.md) | Cross-Hub Resolver & Link Enforcement | 🔴 High | `common/services` | `[x]` |
| [`S6-02d`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-02d.md) | Hub, Membership & Link Management API | 🔴 High | `gateway/api` | `[x]` |
| [`S6-02e`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-02e.md) | Audit Trail & Hub Isolation Guard Tests | 🔴 High | `common/services`, `tests` | `[x]` |

---

## References

- [`references/logic/hubs.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/hubs.md) §4, §5
- [`references/logic/security.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/security.md) — IDOR & tenancy isolation
- [`references/logic/auth.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/auth.md)
