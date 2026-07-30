# B6-10: Frontend — Workflow & Eval Hub Workspaces + Admin Console

> **Status:** `[x]`  
> **Owner:** `frontend/src/components/hubs/workflow`, `frontend/src/components/hubs/eval`, `frontend/src/components/admin`  
> **Secondary:** `frontend/src/store`, `frontend/src/components/auth`  
> **Complexity:** 🔴 High (6 subtasks)

---

## Objective

Build the two orchestration-side hub workspaces plus the Admin Console. The Workflow Hub becomes a real
workflow library with a versioned editor and run history; the Eval Hub can target any workflow or agent
and replay per-node traces; the Admin Console gives platform admins user, invite, approval and audit
management with the supporting auth screens.

---

## Acceptance Criteria

### Workflow Hub

- [ ] `/hubs/workflow/:hubId/workflows` lists **many** workflows with search, tag filter, status chips, last-run status, and row actions (open, duplicate, export, archive, delete).
- [ ] Creating a workflow opens a name/description/template dialog and routes straight into the editor.
- [ ] The editor header shows explicit save state — `Saved • v7 draft`, `Unsaved changes`, `Publishing…`, `Conflict` — never an ambiguous silent state.
- [ ] Draft autosave debounces on 2s idle and sends `If-Match`; a `409` opens a non-destructive conflict dialog offering *Reload server version* or *Copy my changes*.
- [ ] A version rail lists versions with author, note and timestamp; any two can be diffed (nodes/edges added, removed, changed) and any version restored as a new draft.
- [ ] An always-visible validation strip lists graph errors; clicking one focuses and highlights the offending node.
- [ ] The node property drawer's agent/collection pickers group by source hub and show a badge plus "Request link" action when a hub link is missing.
- [ ] Keyboard shortcuts: `Cmd+S` publish, `Cmd+D` duplicate node, `Del` delete, `Cmd+Z` / `Cmd+Shift+Z` undo/redo with a bounded history stack.
- [ ] Navigating away with unsaved changes prompts for confirmation.
- [ ] Runs view lists run history with status, trigger, duration and node count, and drills into an ordered per-node trace timeline with input/output state.

### Eval Hub

- [ ] `/hubs/eval/:hubId` overview shows suite count, recent run scores and pass-rate trend.
- [ ] Suite manager supports create/edit/delete with a **target picker** that switches between *Agent* and *Workflow*, grouped by source hub, filtered to linked hubs only.
- [ ] Test case editor supports final-output assertions and, for workflow targets, node-level assertions with a node selector populated from the target workflow's published graph.
- [ ] Run configuration modal selects framework (RAGAS / DeepEval / both), metric subset and thresholds.
- [ ] Results dashboard shows per-metric aggregates, historical trend charts, and per-case drill-down.
- [ ] For workflow-targeted runs, a trace replay view renders the node timeline with per-node assertion pass/fail overlaid.
- [ ] CSV/JSON import and export are available on every suite.

### Admin Console

- [ ] `/admin/users` directory with status chips, platform-role selector, hub-membership summary, free-text search, status filter and bulk selection.
- [ ] `/admin/users/pending` approval queue supporting approve (with inline role + hub grant assignment) and reject (with optional reason).
- [ ] `/admin/invites` supports single and bulk (paste-a-list) invitation, per-invite role and hub grants, resend, revoke, and a prominent **Copy invite link** fallback with a warning banner when SMTP is unconfigured.
- [ ] `/admin/audit` renders a virtualised, filterable log with an expandable before/after diff.
- [ ] Auth screens exist for register, invite acceptance (dual OAuth/password), pending approval (with polling), suspended/rejected states, and password reset.
- [ ] Self-destructive admin actions are blocked in the UI with an explanatory tooltip, matching the server guardrails.

---

## Linked Subtasks

| ID | Title | File |
|---|---|---|
| S6-10a | Workflow Hub Overview & Workflow Library | [`S6-10a.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-10a.md) |
| S6-10b | Versioned Workflow Editor | [`S6-10b.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-10b.md) |
| S6-10c | Workflow Runs & Trace Drill-Down | [`S6-10c.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-10c.md) |
| S6-10d | Eval Hub Workspace & Target Picker | [`S6-10d.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-10d.md) |
| S6-10e | Eval Results Dashboard & Trace Replay | [`S6-10e.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-10e.md) |
| S6-10f | Admin Console & Auth Screens | [`S6-10f.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-10f.md) |

---

## References

- [`references/logic/workflow_v6.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/workflow_v6.md) §5
- [`references/logic/user_management.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/user_management.md) §6
- [`references/logic/evalops.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/evalops.md)
- [`references/structure/frontend.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/structure/frontend.md)
- [`references/structure/design_system.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/structure/design_system.md)
