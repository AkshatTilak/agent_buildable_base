# B6-09: Frontend — Ingestion & Agent Hub Workspaces

> **Status:** `[x]`  
> **Owner:** `frontend/src/components/hubs/ingestion`, `frontend/src/components/hubs/agent`  
> **Secondary:** `frontend/src/store`, `frontend/src/types`  
> **Complexity:** 🔴 High (5 subtasks)

---

## Objective

Build the two data-side hub workspaces inside the `HubShell` from `B6-08`. The Ingestion Hub gains a
collections workspace, a datastore-binding workspace, and reworked document/job views — all hub-scoped.
The Agent Hub gains an agent library and a full agent detail workspace covering configuration,
collection bindings, endpoint, and invocation history.

---

## Acceptance Criteria

### Ingestion Hub

- [ ] `/hubs/ingestion/:hubId` overview shows collection count, document count, storage footprint, active jobs, binding health, and recent activity.
- [ ] Collections workspace lists collections as cards/rows with vector count, embedding model, dimension, strategy, and last-ingest time; supports create, rename, delete (with typed confirmation) and inline search.
- [ ] The collection creation modal picks the embedding model from the registry, auto-fills dimension, and lets the user choose which datastore bindings back the collection.
- [ ] A collection detail view shows its documents, its retrieval strategy configuration, and an embedded retrieval tester scoped to that collection.
- [ ] Datastores workspace lists bindings with type icon, masked URI, default badge, and live health chip; supports create, edit, test-connection, set-default and delete.
- [ ] Credentials are write-only in the UI — never rendered back after save; the form makes this explicit.
- [ ] Deleting a binding that backs collections is blocked with a clear explanation listing the dependents.
- [ ] Documents view is filterable by collection, type and status, supports bulk re-index and bulk delete, and shows a chunk preview drawer.
- [ ] Job tracker is hub-scoped, streams live status, and supports retry and cancel.

### Agent Hub

- [ ] `/hubs/agent/:hubId` overview shows agent count, invocation volume, error rate, and top agents.
- [ ] Agent library lists agents with model, status, endpoint slug and last-invoked; supports create, duplicate, enable/disable and delete.
- [ ] Agent detail is tabbed: **Configuration** (prompt, model, temperature, max tokens, tools), **Knowledge** (collection bindings), **Endpoint** (slug, hub-qualified external identifier, copy-ready curl snippet), **Invocations** (paginated log with drill-down), **Test** (inline invoke panel with streaming output).
- [ ] The Knowledge tab's collection picker only offers collections from linked ingestion hubs, groups options by hub, and shows an inline "Request link" affordance when no link exists.
- [ ] The system-prompt editor has a monospace surface, character/token estimate, and unsaved-change protection.
- [ ] All create/edit/delete controls are hidden for `viewer` role and disabled with a tooltip when the hub is archived.

---

## Linked Subtasks

| ID | Title | File |
|---|---|---|
| S6-09a | Ingestion Hub Overview & Collections Workspace | [`S6-09a.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-09a.md) |
| S6-09b | Datastore Bindings Workspace | [`S6-09b.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-09b.md) |
| S6-09c | Hub-Scoped Documents & Job Tracker | [`S6-09c.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-09c.md) |
| S6-09d | Agent Hub Overview & Agent Library | [`S6-09d.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-09d.md) |
| S6-09e | Agent Detail Workspace | [`S6-09e.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-09e.md) |

---

## References

- [`references/structure/frontend.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/structure/frontend.md)
- [`references/structure/design_system.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/structure/design_system.md)
- [`references/logic/syntraflow.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/syntraflow.md)
- [`references/logic/guardroute.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/guardroute.md)
