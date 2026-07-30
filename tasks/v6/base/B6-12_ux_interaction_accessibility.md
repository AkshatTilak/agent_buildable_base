# B6-12: UX, Interaction & Accessibility Uplift

> **Status:** `[x]`  
> **Owner:** `frontend/src`  
> **Secondary:** `agent_buildable_base/references/structure/design_system.md`  
> **Complexity:** 🟡 Medium (6 subtasks)

---

## Objective

The hub rework touches every screen; this base task ensures the result is **better to use**, not merely
reorganised. Extend the design token system for hub identity and density, consolidate repeated UI into a
proper shared component layer, standardise loading/empty/error states across every surface, and deliver
a genuine keyboard, accessibility, responsiveness and performance pass.

Runs in parallel with `B6-08` … `B6-10`; its shared components should land early so the hub workspaces
consume them rather than reinventing them.

---

## Acceptance Criteria

### Tokens & components

- [x] Design tokens gain hub accent ramps (one per hub type plus user-selectable accents), status colours (`pending`, `active`, `suspended`, `draft`, `published`, `archived`, `failed`), and a density scale.
- [x] A shared `DataTable` handles sorting, column sizing, sticky header, row selection, bulk-action bar, pagination and keyboard navigation, and is used by every list surface in V6.
- [x] Shared `EmptyState`, `ErrorState`, `LoadingState`, `PageHeader`, `Drawer`, `Tabs`, `Chip`, `Tooltip` and `CopyButton` components exist and are adopted; ad-hoc local duplicates are removed.
- [x] `ConfirmModal` supports a destructive variant requiring the user to type the resource name.

### States & feedback

- [x] Every async surface renders skeletons matching final layout — no layout shift on load, no bare spinners for content regions.
- [x] Every list has a purposeful empty state with an explanatory line and a primary action (e.g. "No collections yet — create your first collection").
- [x] Every error state shows a human-readable message, a correlation/trace id, and a retry action; raw stack traces and bare status codes never reach the user.
- [x] Backend error codes (`HUB_LINK_REQUIRED`, `HUB_LINK_REVOKED`, `ACCOUNT_PENDING_APPROVAL`, `HUB_ARCHIVED`, conflict `409`s) each map to a specific, actionable message.
- [x] Optimistic updates with rollback on failure are used for toggles, renames and reorders; toasts confirm success and describe failures.

### Keyboard & accessibility

- [x] All interactive elements are reachable and operable by keyboard with a visible focus ring meeting contrast requirements.
- [x] Modals and drawers trap focus, restore focus on close, and close on `Esc`.
- [x] Icon-only buttons have accessible labels; tables use proper semantics; live regions announce async status changes.
- [x] Colour is never the sole carrier of meaning — status chips pair colour with a label or icon.
- [x] Text contrast meets WCAG AA across the dark theme; a documented shortcut sheet is reachable via `?`.

### Responsiveness & performance

- [x] Layouts hold from 1280px to 2560px without truncation or overflow; the sidebar collapses gracefully below 1024px.
- [x] Long lists (documents, invocations, audit log, traces) are virtualised.
- [x] Route-level code splitting is applied; the workflow editor and its ReactFlow dependency load lazily.
- [x] No text clipping or unintended truncation regressions relative to the V4 typography work; long hub, collection, agent and workflow names truncate with a tooltip revealing the full value.
- [x] Contextual onboarding: first-run guidance on the Hub Directory, an inline explainer for hub links, and a "what is a hub?" help affordance in the shell.

---

## Linked Subtasks

| ID | Title | File |
|---|---|---|
| S6-12a | Design Token Extension for Hubs & Status | [`S6-12a.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-12a.md) |
| S6-12b | Shared Component Layer & `DataTable` | [`S6-12b.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-12b.md) |
| S6-12c | Loading, Empty & Error State Standardisation | [`S6-12c.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-12c.md) |
| S6-12d | Keyboard & Accessibility Pass | [`S6-12d.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-12d.md) |
| S6-12e | Onboarding & Contextual Guidance | [`S6-12e.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-12e.md) |
| S6-12f | Responsiveness & Performance Pass | [`S6-12f.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-12f.md) |

---

## References

- [`references/structure/design_system.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/structure/design_system.md)
- [`references/structure/frontend.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/structure/frontend.md)
- [`tasks/v4/base/01_frontend_spacing_layout.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v4/base/01_frontend_spacing_layout.md) — do not regress V4 typography work
