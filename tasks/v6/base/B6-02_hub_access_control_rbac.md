# B6-02: Hub Access Control, Scoping & RBAC v2

> **Status:** `[ ]`  
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

- [ ] `users.role` is replaced by `users.platform_role` (`admin` | `member`); V5 values are mapped `admin→admin`, `editor→member`, `viewer→member`.
- [ ] `gateway/auth/hub_context.py` exposes `HubContext` and the `require_hub(hub_type=..., min_role=...)` dependency factory.
- [ ] Failure semantics match `hubs.md` §5.2 exactly — non-members receive `404`, never `403`, so hub existence cannot be enumerated.
- [ ] Platform `admin` short-circuits every hub check and is treated as `owner`.
- [ ] Archived hubs reject mutating requests with `409`.
- [ ] `common/services/hub_resolver.py` validates every cross-hub reference against `hub_links`, enforces the allowed-direction matrix, and re-validates at execution time.
- [ ] Hub links are non-transitive — a test proves an indirect grant does not confer access.
- [ ] `gateway/api/hubs.py` implements hub CRUD, membership CRUD, link CRUD, and archive/transfer-ownership, all audited.
- [ ] The last `owner` of a hub cannot be removed or demoted; ownership transfer is the only path.
- [ ] Every mutating hub-scoped endpoint writes exactly one `audit_log` row via shared middleware or a decorator.
- [ ] `tests/test_hub_isolation.py` proves that for each hub-scoped resource type, a member of hub A receives `404` for a resource id belonging to hub B across every route.
- [ ] A static guard test fails the build if any hub-scoped model is queried by primary key alone in `gateway/` or `projects/`.

---

## Linked Subtasks

| ID | Title | File |
|---|---|---|
| S6-02a | Platform Role Refactor & Role Constants | [`S6-02a.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-02a.md) |
| S6-02b | `HubContext` Dependency & `require_hub` Factory | [`S6-02b.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-02b.md) |
| S6-02c | Cross-Hub Resolver & Link Enforcement | [`S6-02c.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-02c.md) |
| S6-02d | Hub, Membership & Link Management API | [`S6-02d.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-02d.md) |
| S6-02e | Audit Trail & Hub Isolation Guard Tests | [`S6-02e.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-02e.md) |

---

## References

- [`references/logic/hubs.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/hubs.md) §4, §5
- [`references/logic/security.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/security.md) — IDOR & tenancy isolation
- [`references/logic/auth.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/auth.md)
