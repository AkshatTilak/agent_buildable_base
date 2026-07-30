# B6-08: Frontend — Hub Shell, Information Architecture & Navigation

> **Status:** `[x]`  
> **Owner:** `frontend/src`  
> **Secondary:** `frontend/src/store`, `frontend/src/services`  
> **Complexity:** 🔴 High (6 subtasks)

---

## Objective

Rebuild the frontend's information architecture around hubs. Everything is entered through the **Hub
Directory** or the **Hub Switcher**; every hub renders inside a shared **Hub Shell** that provides
consistent sub-navigation, breadcrumbs, membership and link management. This base task delivers the
shell, routing, navigation, state layer and API client — the four hub workspaces themselves land in
`B6-09` and `B6-10`.

---

## Acceptance Criteria

- [ ] React Router is restructured to the V6 tree: `/hubs`, `/hubs/new`, `/hubs/:hubType/:hubId/*`, `/admin/*`, and the platform surfaces (`/playground`, `/mcp`, `/infrastructure`, `/settings`, `/system`).
- [ ] All route paths are centralised in `frontend/src/routes.ts` as typed builders — no hardcoded path strings in components.
- [ ] `/` redirects to `/hubs`; unknown hub ids render a dedicated not-found state, not a crash.
- [ ] `HubShell.tsx` provides the persistent frame: hub header (name, type badge, accent, archived banner), type-specific tab bar, breadcrumbs, and an outlet for the workspace.
- [ ] `HubShell` loads the hub once and exposes it plus the caller's `hub_role` through context; child views never re-fetch the hub.
- [ ] UI affordances are gated by `hub_role` — a `viewer` sees no create/edit/delete controls at all, rather than controls that fail on click.
- [ ] The sidebar is regrouped into **Hubs** (four expandable type groups listing the user's hubs), **Platform**, and **Admin** (platform admins only, with a live pending-approval badge).
- [ ] A global Hub Switcher (`Cmd+K`) searches across every hub the user belongs to and jumps directly to it; recent hubs are surfaced first.
- [ ] The Command Palette is extended with hub-aware actions (create hub, create workflow in current hub, invite user, jump to collection).
- [ ] `frontend/src/store/hubSlice.ts` holds hub lists by type, the active hub, membership and links; hub-scoped slices key their data by `hubId`.
- [ ] `frontend/src/services/api.ts` is refactored so every hub-scoped call takes `hubId` as an explicit first argument; flat endpoint helpers are **removed**.
- [ ] The Hub Directory lists all four types with search, type filter, archived toggle, resource counts, member avatars and last-activity.
- [ ] The Hub creation wizard collects type, name, slug (auto-derived, editable, uniqueness-checked), description, accent/icon, initial members and initial links.
- [ ] Shared `MembersPanel` and `HubLinksPanel` components work identically in all four hub types, with the link direction matrix enforced in the UI.
- [ ] The frontend builds with zero TypeScript errors and no unused legacy route or component remains.

---

## Linked Subtasks

| ID | Title | File |
|---|---|---|
| S6-08a | Router Restructure & Typed Route Builders | [`S6-08a.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-08a.md) |
| S6-08b | Hub Shell Layout, Context & Role Gating | [`S6-08b.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-08b.md) |
| S6-08c | Sidebar, Hub Switcher & Command Palette | [`S6-08c.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-08c.md) |
| S6-08d | `hubSlice` & Hub-Scoped API Client | [`S6-08d.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-08d.md) |
| S6-08e | Hub Directory & Creation Wizard | [`S6-08e.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-08e.md) |
| S6-08f | Shared Members & Links Panels | [`S6-08f.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-08f.md) |

---

## References

- [`references/structure/frontend.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/structure/frontend.md) — V6 hub shell IA
- [`references/structure/design_system.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/structure/design_system.md)
- [`references/logic/hubs.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/hubs.md) §4, §8
