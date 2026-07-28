# V6 Goal: The Hub Platform — Multi-Tenant Grouping, Multi-Workflow Authoring & Managed Access

> **Version:** 6
> **Status:** `[ ] In Progress`
> **Depends On:** V1–V5 (Completed)
> **Cutover Policy:** Hard — legacy flat routes, components and columns are **deleted**, not deprecated.

---

## North Star Objective

Restructure ContAIned from a flat, single-workspace platform into a **hub-organised, multi-tenant AI
operations platform**. Every domain resource moves inside a typed, isolated **Hub**; the platform grows
proper multi-workflow authoring; and administrators gain full lifecycle control over who may enter the
system and what they may touch.

### The Four Base Hubs

Everything the user interacts with is entered through one of four hub types. A deployment may hold many
hubs of each type.

1. **Ingestion Hub** — create and manage many collections of data *and* the databases that store them
   (Qdrant / Neo4j / Postgres / OpenSearch bindings), plus documents, jobs, and retrieval configuration.
2. **Agent Hub** — create and manage agents, their prompts, models, tools, endpoints, and the
   collections they may retrieve from.
3. **Workflow Hub** — create and manage **many** workflows, each independently versioned, runnable, and
   composed from agents in linked agent hubs plus native logic and action blocks.
4. **Eval Hub** — create evaluations that target **any** workflow **or** agent, with full intermediate
   flow tracing.

### The Five Pillars

1. **Hub Tenancy Foundation** — a `hubs` / `hub_members` / `hub_links` model, `hub_id` on every domain
   table, and mandatory query scoping. Cross-hub consumption is only possible through explicit,
   directional, revocable links.
2. **Managed Access** — platform roles (`admin` / `member`) plus per-hub roles
   (`owner` / `maintainer` / `contributor` / `viewer`), admin-issued invitations, local password auth
   alongside Google/GitHub SSO, and an approval gate that holds every unsolicited sign-up until an
   admin approves it.
3. **Multi-Workflow Authoring** — the singleton canvas becomes a real workflow library with immutable
   versions, draft/publish lifecycle, run history, duplication, import/export, and conflict-safe editing.
4. **Coherent Rework, Not Accretion** — database structures, backend routes, frontend views, stores and
   types are reworked together. Legacy code is removed in the same version that replaces it, and the
   removal is itself verified.
5. **Usability Uplift** — a consistent hub shell, hub switcher, command palette, standardised
   loading/empty/error states, keyboard and accessibility coverage, and contextual onboarding across
   every surface.

---

## What "Done" Looks Like

A user signs in (SSO or password). If they were invited, they land directly in the hubs granted to
them; if they self-registered, they see a clear pending-approval screen until an admin approves them.
An admin opens the Admin Console, reviews the approval queue, invites three colleagues by email, and
assigns each of them a role in specific hubs — with a copyable link fallback if SMTP is not configured.

From the Hub Directory, a maintainer creates a `Support KB` ingestion hub, points it at a dedicated
Qdrant binding, and creates three collections inside it. They create a `Support Agents` agent hub, link
it to `Support KB`, and build two agents that retrieve from those collections. They create a
`Support Flows` workflow hub, link it to `Support Agents`, and author four separate workflows in it —
each versioned, each publishable, each with its own run history. Finally, they create a
`Support QA` eval hub, link it to both the workflow and agent hubs, and build suites that evaluate a
whole workflow end-to-end *and* individual agents, asserting on intermediate node state.

Nothing leaks across hub boundaries. No flat legacy route answers. The old singleton workflow store,
the old `admin/editor/viewer` global roles, and the `tenant_id` column no longer exist anywhere in the
codebase.

---

## Completion Criteria

This goal is met when:

- [ ] All Base Tasks `B6-01` … `B6-12` and every linked Subtask are marked `[x]`.
- [ ] Every hub-scoped table has a `NOT NULL hub_id` and every query against it filters by `hub_id`.
- [ ] `tests/test_hub_isolation.py` passes: no cross-hub read or write is reachable through any route.
- [ ] The forward migration runs cleanly on a populated V5 database, is idempotent on re-run, and
      `downgrade()` restores a working V5 schema.
- [ ] No route, component, column, or store field listed in
      [`references/logic/hubs.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/hubs.md) §9
      remains anywhere in the repository (verified by grep in `S6-11a` / `S6-11b`).
- [ ] The frontend builds with zero TypeScript errors and every route in the V6 IA resolves.
- [ ] An admin can invite, approve, suspend, and reinstate a user end-to-end, with and without SMTP.
- [ ] A workflow hub holds ≥ 2 concurrently editable, independently versioned, independently runnable
      workflows.
- [ ] An eval suite can target both an agent and a workflow and produce per-node traces for the latter.
- [ ] All reference documents under `references/` describe the V6 architecture with no stale V5 claims.

---

## Key References

| Document | Role in V6 |
|---|---|
| [`references/logic/hubs.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/hubs.md) | **Canonical** tenancy model — schema, roles, scoping contract, migration plan |
| [`references/logic/user_management.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/user_management.md) | Invites, approval gate, password auth, admin console |
| [`references/logic/workflow_v6.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/workflow_v6.md) | Multi-workflow management, versioning, runs |
| [`references/structure/frontend.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/structure/frontend.md) | V6 hub shell IA, routing, store shape |
| [`references/structure/system_architecture.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/structure/system_architecture.md) | Scoping rules, env vars, migration ordering |
| [`references/logic/security.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/security.md) | Tenancy isolation & IDOR prevention rules |
