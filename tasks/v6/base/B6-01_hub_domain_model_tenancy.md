# B6-01: Hub Domain Model & Tenancy Foundation

> **Status:** `[ ]`  
> **Owner:** `common/models`, `migrations`  
> **Secondary:** `common/schemas`, `common/services`  
> **Complexity:** 🔴 High (6 subtasks)

---

## Objective

Introduce the **Hub** as the platform's unit of tenancy and land the complete database foundation for
it. This base task creates the `hubs`, `hub_members`, `hub_links`, `datastore_bindings` and `audit_log`
tables, adds a `hub_id` foreign key to every domain table listed in
[`hubs.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/hubs.md) §3.6, and ships a
reversible two-stage Alembic migration that seeds four default hubs and backfills every existing row
into them without data loss.

This is the **foundational** base task. `B6-02` through `B6-11` all depend on it; nothing else in V6 may
begin until `S6-01a` … `S6-01e` are complete.

---

## Acceptance Criteria

- [ ] `Hub`, `HubMember`, `HubLink`, `DatastoreBinding` and `AuditLog` SQLAlchemy models exist in `common/models/database.py` with the exact columns, indexes and constraints defined in `hubs.md` §3.
- [ ] `hub_type` is constrained to `ingestion | agent | workflow | eval` and is immutable after creation.
- [ ] `hub_role` is constrained to `owner | maintainer | contributor | viewer` with a documented ordering helper.
- [ ] Every table in `hubs.md` §3.6 carries a `hub_id` column with the specified nullability.
- [ ] Uniqueness constraints are rebuilt per `hubs.md` §3.7 (`(hub_id, endpoint_slug)`, `(hub_id, name)` for collections / workflows / eval suites).
- [ ] `syntraflow_collections.tenant_id` is dropped; `physical_name` is added and populated as `{hub_slug}__{name}`.
- [ ] Migration stage 1 (additive) and stage 2 (backfill + constrain + drop) both run cleanly against a populated V5 database.
- [ ] The migration seeds four default hubs, seeds all allowed hub links between them, and enrols every existing active user with the mapped hub role.
- [ ] `downgrade()` is implemented for both stages and restores a working V5 schema.
- [ ] Re-running the migration against an already-migrated database is a no-op (idempotent guards on seed inserts).
- [ ] Pydantic schemas for hub, member, link, binding and audit entities exist in `common/schemas/hubs.py`.
- [ ] A `HubRepository` in `common/services/hub_repository.py` exposes scoped list/get/create/update/archive helpers that **require** `hub_id` on every read path.

---

## Linked Subtasks

| ID | Title | File |
|---|---|---|
| S6-01a | Hub, HubMember & HubLink Models | [`S6-01a.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-01a.md) |
| S6-01b | DatastoreBinding & AuditLog Models | [`S6-01b.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-01b.md) |
| S6-01c | Propagate `hub_id` Across Domain Models | [`S6-01c.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-01c.md) |
| S6-01d | Migration Stage 1 — Additive Schema | [`S6-01d.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-01d.md) |
| S6-01e | Migration Stage 2 — Seed, Backfill & Constrain | [`S6-01e.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-01e.md) |
| S6-01f | Hub Schemas & Scoped Repository Layer | [`S6-01f.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-01f.md) |

---

## References

- [`references/logic/hubs.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/hubs.md) §3, §6
- [`references/structure/system_architecture.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/structure/system_architecture.md) — Database & migration ordering
- [`references/code/common_library.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/code/common_library.md) — model & client conventions
