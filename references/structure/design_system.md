# Frontend Design System & Component Pattern Guide (V6)

> **Source:** ContAIned V3 Design Language Specification, extended for V6 hub identity, status and density
> **Last Updated:** 2026-07-28
> **Authority:** All frontend changes MUST comply with this design system. Deviation requires explicit approval.
> **Additive rule:** every V3/V4 token name is preserved verbatim. V6 tokens are **additive only** — no `--bg-*`, `--text-*`, `--space-*`, `--radius-*` or `--transition-*` token is renamed, so the V4 spacing and typography work does not regress.

---

## 1. Design Philosophy

ContAIned V3 follows a **"Dark Control Plane"** aesthetic — a premium, developer-facing admin dashboard with a dense information hierarchy, neon-accent glassmorphism, and micro-animated feedback loops. Think: Vercel Dashboard meets Datadog meets Linear.

### Core Principles
1. **Information Density:** Dense but scannable. Use condensed type scales and compact spacing.
2. **Depth via Layering:** Use backdrop-blur glassmorphism to create z-depth without shadows.
3. **Signal via Color:** Color is reserved for status, accent, and emphasis — NEVER decorative.
4. **Motion as Feedback:** Animations confirm user actions. Idle UI should be calm, not distracting.
5. **Consistency over Creativity:** Every card, button, and input must use the token system below.

---

## 2. Color Palette (CSS Custom Properties)

All colors must be referenced as `var(--token-name)`. Never hardcode hex values in components.

### Backgrounds (Layered Depth System)
```
--bg-deep:         #080809       // Page body background
--bg-main:         #0E0F12       // Main content area
--bg-surface:      #14151A       // Cards, panels, drawers
--bg-surface-alt:  #181A21       // Alternate surface (modals, dropdowns)
--bg-elevated:     #1E2028       // Hover states, tooltips
--bg-input:        #121316       // Form input backgrounds
```

### Borders
```
--border-subtle:   rgba(255, 255, 255, 0.05)    // Default card borders
--border-default:  rgba(255, 255, 255, 0.08)    // Visible separators
--border-hover:    rgba(255, 255, 255, 0.12)    // Hover state borders
--border-focus:    var(--accent-indigo)           // Focus ring color
```

### Typography Colors
```
--text-primary:    #F4F5F7       // Headings, important content
--text-secondary:  #9FA2B2       // Body text, descriptions
--text-muted:      #56596B       // Hints, timestamps, metadata
--text-disabled:   #3A3D4A       // Disabled inputs/buttons
```

### Accent Colors (Status-Semantic)
Each accent has three variants: solid, soft (10% opacity bg), and glow (for shadows/rings).

| Token | Hex | Use Case |
|---|---|---|
| `--accent-emerald` | `#10B981` | Success, active, connected, primary CTA |
| `--accent-indigo` | `#6366F1` | Focus rings, selected items, links |
| `--accent-amber` | `#F59E0B` | Warning, degraded, in-progress |
| `--accent-cyan` | `#06B6D4` | Informational, secondary data |
| `--accent-rose` | `#EF4444` | Error, destructive, disconnected |

### Soft Backgrounds (for badges, tags, status pills)
```
--emerald-soft:    rgba(16, 185, 129, 0.10)
--indigo-soft:     rgba(99, 102, 241, 0.10)
--amber-soft:      rgba(245, 158, 11, 0.10)
--cyan-soft:       rgba(6, 182, 212, 0.10)
--rose-soft:       rgba(239, 68, 68, 0.10)
```

### Hub Accents (V6)

Every hub type carries a default identity accent so a user can tell at a glance which kind of workspace
they are in. Each ramp has a base, a `-soft` background and a `-glow` shadow variant.

| Token | Resolves to | Hub type |
|---|---|---|
| `--hub-ingestion` | `var(--accent-cyan)` | `ingestion` |
| `--hub-agent` | `var(--accent-indigo)` | `agent` |
| `--hub-workflow` | `var(--accent-emerald)` | `workflow` |
| `--hub-eval` | `var(--accent-amber)` | `eval` |
| `--hub-archived` | `var(--text-muted)` | any archived hub or resource |

A hub may override its default with a **user-selectable accent** stored on `hubs.accent`
([`hubs.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/hubs.md) §3.1). The stored
key resolves to `--accent-{key}` from the extended palette:

```
--accent-violet   --accent-teal   --accent-pink   --accent-lime   --accent-slate
   (each with matching -soft and -glow variants)
```

**Resolution rule:** `HubShell` sets `data-hub-accent="{key}"` on its root element, which binds
`--hub-accent`, `--hub-accent-soft` and `--hub-accent-glow`. Every hub-scoped component references
`var(--hub-accent)` and **never** a literal hex. Enforcement:
`grep -rnE "#[0-9a-fA-F]{6}" frontend/src/components/hubs` must return zero hits.

### Status Tokens (V6)

Every status the platform renders has a token triple: base, `-soft` background, and `-fg` foreground
guaranteed to meet WCAG AA (≥ 4.5:1) on that background.

| Token | Domain | Base accent |
|---|---|---|
| `--status-pending` | user account, invite | amber |
| `--status-active` | user account, agent, binding | emerald |
| `--status-suspended` | user account | amber |
| `--status-rejected` | user account, invite | rose |
| `--status-draft` | workflow, eval suite | slate |
| `--status-published` | workflow | emerald |
| `--status-archived` | hub, workflow, collection | muted |
| `--status-queued` | job, workflow run, eval run | cyan |
| `--status-running` | job, workflow run, eval run | indigo |
| `--status-succeeded` | job, workflow run, eval run | emerald |
| `--status-failed` | job, workflow run, eval run | rose |
| `--status-cancelled` | job, workflow run | muted |

Status is always rendered through the shared `Chip` component, never as a raw coloured dot.

---

## 3. Typography

### Font Stack
```
--font-sans:     'Inter', system-ui, sans-serif      // Body text
--font-display:  'Outfit', system-ui, sans-serif      // Headings, brand
--font-mono:     'JetBrains Mono', monospace          // Code, IDs, metrics
```

### Type Scale (rem-based)
| Token | Size | Weight | Use |
|---|---|---|---|
| `display-lg` | 1.5rem | 700 | Page titles only |
| `heading-md` | 1rem | 600 | Section headers |
| `heading-sm` | 0.875rem | 600 | Card titles |
| `body-md` | 0.875rem | 400 | Primary body text |
| `body-sm` | 0.8125rem | 400 | Descriptions |
| `caption` | 0.75rem | 500 | Labels, badges |
| `micro` | 0.6875rem | 500 | Timestamps, metadata |
| `mono-data` | 0.75rem | 400, mono | Numeric data, IDs |

### Rules
- **Headings:** Always `font-display` (Outfit), never `font-sans`.
- **Labels/Badges:** Always `uppercase`, `letter-spacing: 0.05em`, `caption` size.
- **Numeric Data:** Always `font-mono`, right-aligned in tables.
- **Truncation:** Use `text-overflow: ellipsis` with `max-width`. Never wrap long IDs.
- **Truncation + tooltip (V6):** long hub, collection, agent, workflow, suite and document names truncate
  with an ellipsis and expose the full value through the shared `Tooltip` on hover **and** on keyboard
  focus. A truncated name is never the only place a value appears. This rule must not regress the V4
  typography work — no new text clipping, and layouts hold from 1280px to 2560px.

---

## 4. Spacing & Layout Tokens

### Spacing Scale (used for padding, margin, gap)
```
--space-xs:   0.25rem   (4px)
--space-sm:   0.5rem    (8px)
--space-md:   0.75rem   (12px)
--space-lg:   1rem      (16px)
--space-xl:   1.5rem    (24px)
--space-2xl:  2rem      (32px)
```

### Layout Rules
- **Sidebar:** Fixed 260px width. Collapsible to 64px (icon-only).
- **Content area:** Flex-grow, padding `--space-xl`.
- **Card gap:** `--space-lg` between cards in grids.
- **Card padding:** `--space-xl` internal padding.
- **Section gap:** `--space-xl` between dashboard sections.
- **Form gap:** `--space-md` between form fields.

### Border Radius
```
--radius-sm:   6px      // Buttons, inputs, badges
--radius-md:   8px      // Small cards, dropdowns
--radius-lg:   12px     // Main cards, panels
--radius-xl:   16px     // Modals, upload zones
--radius-full: 9999px   // Pills, avatars, dots
```

### Density Scale (V6)

Information density is a user preference, not a per-screen decision. A `data-density` attribute on
`<body>` selects a mode; the value is persisted in `frontend/src/store/settingsSlice.ts` and toggled
from Settings.

| Token | `comfortable` (default) | `compact` |
|---|---|---|
| `--row-height` | 44px | 34px |
| `--control-height` | 36px | 30px |
| `--table-cell-padding-y` | `var(--space-md)` | `var(--space-sm)` |
| `--section-gap` | `var(--space-xl)` | `var(--space-lg)` |

Tables, lists and form controls read these tokens rather than hardcoding heights, so both modes are
reachable without per-component overrides.

### Focus & Elevation Tokens (V6)
```
--focus-ring:         2px solid var(--accent-indigo)
--focus-ring-offset:  2px
--shadow-drawer:      0 0 40px rgba(0, 0, 0, 0.45)
--shadow-modal:       0 24px 60px rgba(0, 0, 0, 0.55)
--overlay-scrim:      rgba(0, 0, 0, 0.70)
```

---

## 5. Component Patterns

### 5.1 Glass Card (Primary Container)
Every content section is wrapped in a Glass Card:
```css
.glass-card {
  background: var(--bg-surface);
  backdrop-filter: blur(14px);
  border: 1px solid var(--border-subtle);
  border-radius: var(--radius-lg);
  padding: var(--space-xl);
  transition: border-color 0.25s ease, background 0.25s ease;
}
.glass-card:hover {
  border-color: var(--border-hover);
  background: var(--bg-elevated);
}
```

### 5.2 Buttons
Three tiers — Primary, Secondary, Ghost:

| Variant | Background | Border | Text | Shadow |
|---|---|---|---|---|
| **Primary** | `--accent-emerald` | 1px `emerald/50%` | white | `0 4px 14px emerald/30%` |
| **Secondary** | `--accent-indigo` | 1px `indigo/50%` | white | `0 4px 14px indigo/30%` |
| **Ghost** | transparent | 1px `--border-subtle` | `--text-secondary` | none |
| **Danger** | `--accent-rose` | 1px `rose/50%` | white | `0 4px 14px rose/30%` |

All buttons: `border-radius: var(--radius-sm)`, `font-weight: 600`, `font-size: caption`, hover `translateY(-1px)`.

### 5.3 Form Inputs
```css
.input {
  background: var(--bg-input);
  border: 1px solid var(--border-subtle);
  border-radius: var(--radius-md);
  padding: var(--space-md) var(--space-lg);
  color: var(--text-primary);
  font-size: body-sm;
  transition: border-color 0.25s, box-shadow 0.25s;
}
.input:focus {
  border-color: var(--accent-indigo);
  box-shadow: 0 0 0 3px var(--indigo-soft);
  outline: none;
}
```

### 5.4 Status Indicators
- **Connection Dot:** 8px circle with matching glow shadow + pulse animation.
- **Badge:** Pill shape (`--radius-full`), soft bg + solid text + 1px matching border.
- **Progress Bar:** Full-width, 6px height, `--radius-full`, animated width transition.

### 5.5 Tables
```
- Header row: bg var(--bg-surface-alt), text var(--text-muted), uppercase caption size
- Body rows: bg var(--bg-input), hover bg var(--bg-surface-alt)
- Dividers: 1px solid var(--border-subtle) between rows
- Numeric columns: right-aligned, font-mono
- Action columns: right-aligned, icon buttons only
```

### 5.6 Modals / Drawers
```
- Overlay: bg black/70%, backdrop-filter: blur(4px)
- Panel: bg var(--bg-surface), border var(--border-default), radius var(--radius-xl)
- Header: border-bottom separator, close X button top-right
- Footer: border-top separator, action buttons right-aligned
```

### 5.7 Toast Notifications
```
- Position: bottom-right, stacked vertically
- Variants: success (emerald), error (rose), warning (amber), info (indigo)
- Auto-dismiss: 5 seconds with progress bar
- Animation: slide-in from right, slide-out to right
```

### 5.8 Loading Skeletons
```
- Shimmer animation: left-to-right linear gradient sweep
- Match exact dimensions of content being loaded
- Use for: cards, table rows, metric values, charts
- Duration: 1.5s infinite loop
```

### 5.9 Shared Component Layer (V6 — mandatory)

Every V6 surface composes from `frontend/src/components/shared/`. Ad-hoc local re-implementations of
any component below are forbidden; if a surface needs a variation, extend the shared component rather
than forking it.

| Component | Contract |
|---|---|
| `DataTable` | The only list primitive. Sorting, column sizing, sticky header, row selection, bulk-action bar, pagination and full keyboard navigation. **Every** list surface in V6 uses it — no hand-rolled `<table>`. Virtualises above 50 rows. |
| `PageHeader` | Title, optional subtitle, breadcrumb slot, right-aligned action slot. Actions are wrapped in `<Gated>` so role/archive gating is uniform. |
| `EmptyState` | Icon, headline, one explanatory line, and a **primary action**. Never a bare “No data”. |
| `ErrorState` | Human-readable message, correlation/trace id (`font-mono`, with `CopyButton`), and a retry action. |
| `LoadingState` | Layout-matching skeletons. Bare spinners are not permitted for content regions. |
| `Drawer` | Right-side panel; focus trap, focus restore, `Esc` to close, `--shadow-drawer`, `--overlay-scrim`. |
| `Tabs` | Accessible tablist semantics with arrow-key navigation; the active tab is URL-addressable. |
| `Chip` | Status and metadata pill. Takes a status token key and pairs colour with a label or icon. |
| `Tooltip` | Opens on hover **and** keyboard focus; used for truncated values and for disabled-control reasons. |
| `CopyButton` | Copy-to-clipboard with a transient confirmation; used for ids, endpoint slugs, curl snippets and invite links. |
| `ConfirmModal` | Standard and **destructive** variants. The destructive variant requires the user to **type the resource name** before the confirm button enables, and states exactly what will be deleted. |

### 5.10 Standard State Patterns (V6)

Every async surface renders exactly one of four states, using the components above.

* **Loading** — skeletons that match the final layout dimension-for-dimension, so nothing shifts when
  data lands. No bare spinners for content regions; spinners are reserved for in-button pending state.
* **Empty** — purposeful, specific and actionable: an explanatory line plus a primary action
  (*No collections yet — create your first collection*), not a generic placeholder.
* **Error** — a human-readable message, a correlation/trace id, and a retry that re-issues only the
  failed request. Raw stack traces and bare status codes never reach the user. Backend error codes
  (`HUB_LINK_REQUIRED`, `HUB_LINK_REVOKED`, `HUB_ARCHIVED`, `ACCOUNT_PENDING_APPROVAL`, and `409`
  conflicts) each map to a specific, actionable message.
* **Ready** — the content itself.

Mutations use optimistic updates with rollback on failure for toggles, renames and reorders; a toast
confirms success and describes failures in the same vocabulary as the error state.

---

## 6. Animation Specifications

### Transitions
```
--transition-fast:    0.15s cubic-bezier(0.4, 0, 0.2, 1)   // Hover states
--transition-normal:  0.25s cubic-bezier(0.4, 0, 0.2, 1)   // Layout shifts
--transition-slow:    0.4s  cubic-bezier(0.4, 0, 0.2, 1)   // Page transitions
```

### Keyframe Animations
| Name | Use | Duration |
|---|---|---|
| `pulse-ring` | Connection dots, live status | 2s infinite |
| `shimmer` | Loading skeletons | 1.5s infinite |
| `slide-in-right` | Toast entry | 0.3s ease-out |
| `slide-out-right` | Toast exit | 0.2s ease-in |
| `fade-in` | Page transitions, modals | 0.25s ease |
| `count-up` | Metric numbers on change | 0.5s ease-out |

### Rules
- **Hover animations:** `--transition-fast` only. Never use `--transition-slow` for hover.
- **Page transitions:** Use `framer-motion` `AnimatePresence`. Fade + slight Y translate.
- **Interactive elements:** Subtle `scale(1.02)` on hover. Never exceed `scale(1.05)`.
- **Progress bars:** Smooth `width` transition with `--transition-normal`.
- **Scroll animations:** FORBIDDEN. No scroll-triggered animations. Performance cost is too high.

---

## 7. Responsive Breakpoints

```
Desktop:   >= 1280px  (primary target)
Tablet:    >= 1024px  (compact cards, 2-column grids)
Small:     < 1024px   (stack to single column, collapse sidebar)
```

The platform is **desktop-first**. Mobile is NOT a target.

---

## 8. Component File Naming Convention

```
frontend/src/
├── routes.ts             // Typed path builders + ROUTE_PATTERNS (no hardcoded paths elsewhere)
├── components/
│   ├── layout/           // Sidebar, Header, Breadcrumbs, CommandPalette
│   ├── hubs/             // HubShell, HubContext, HubDirectory, HubCreateWizard,
│   │   │                 // HubSwitcher, MembersPanel, HubLinksPanel, Gated, hubTabs.ts
│   │   ├── ingestion/    // IngestionOverview, CollectionsWorkspace, CollectionDetail,
│   │   │                 // DatastoresWorkspace, DocumentsWorkspace, JobsWorkspace
│   │   ├── agent/        // AgentOverview, AgentLibrary, AgentDetail
│   │   ├── workflow/     // WorkflowHubOverview, WorkflowLibrary, WorkflowEditor,
│   │   │                 // VersionRail, ValidationStrip, WorkflowRuns, NodeTraceTimeline
│   │   └── eval/         // EvalHubOverview, SuiteManager, TargetPicker, TestCaseEditor,
│   │                     // RunConfigModal, EvalResults, CaseResultsTable, TraceReplay
│   ├── admin/            // UserDirectory, ApprovalQueue, InviteManager, AuditLogViewer
│   ├── auth/             // LoginPage, RegisterPage, InviteAccept, PendingApproval, ResetPassword
│   ├── dashboard/        // SystemMetrics, ServiceHealth, ModelRegistry
│   ├── nodes/            // ReactFlow node renderers (consumed by the workflow editor)
│   └── shared/           // DataTable, PageHeader, EmptyState, ErrorState, LoadingState,
│                         // Drawer, Tabs, Chip, Tooltip, CopyButton, ConfirmModal, Toast
├── store/                // Zustand slices (hubSlice + one per domain, keyed by hubId)
├── services/             // API client, telemetry, localStorage helpers
├── hooks/                // useHubPermissions, useKeyboard, useDebounce
└── types/                // Shared TypeScript interfaces (replace all `any`)
```

### Naming Rules
- **Components:** PascalCase, `.tsx` extension. One component per file.
- **Hooks:** camelCase with `use` prefix: `useKeyboard.ts`, `useDebounce.ts`.
- **Types:** PascalCase interfaces in dedicated `types/` directory.
- **Store slices:** camelCase with `Slice` suffix: `metricsSlice.ts`.
- **CSS:** Single `index.css` for design tokens + utility classes. Component-specific styles use Tailwind classes inline.

---

## 9. Accessibility & Performance Rules

### 9.1 Accessibility Baseline (V6 — non-negotiable)

- **Keyboard reachability:** every interactive element is reachable and operable by keyboard, in a
  logical tab order. Custom interactive elements set `tabIndex` correctly.
- **Visible focus:** a visible focus ring (`--focus-ring` at `--focus-ring-offset`) on every focusable
  element, meeting contrast requirements against its background. Focus is never suppressed with
  `outline: none` unless an equivalent ring replaces it.
- **Focus management:** modals and drawers **trap** focus while open, **restore** focus to the invoking
  control on close, and close on `Esc`.
- **Labelling:** icon-only buttons carry an accessible label (`aria-label`); all images/icons in an
  interactive context have a `title`; tables use proper `<th scope>` semantics; live regions announce
  async status changes (job progress, run completion, save state).
- **Colour is never the sole carrier of meaning.** Status chips always pair colour with a label or an
  icon, so run states remain distinguishable without colour perception.
- **Contrast:** all text meets WCAG AA (≥ 4.5:1 body, ≥ 3:1 large) across the dark theme. Every
  `--status-*-fg` on its `--status-*-soft` pairing is measured and the ratio recorded.
- **Shortcut sheet:** pressing `?` opens a documented keyboard-shortcut sheet covering global
  (`Cmd+K` hub switcher, command palette) and contextual (workflow editor) shortcuts.

### 9.2 Performance Rules

- No layout shifts on data load (use skeletons that match final dimensions).
- All lists > 50 items must use virtualization (`react-window` or similar) — documents, invocations,
  audit log, traces.
- Route-level code splitting; the workflow editor and its ReactFlow dependency load lazily.
- Layouts hold from 1280px to 2560px without truncation or overflow; the sidebar collapses gracefully
  below 1024px.
- WebSocket reconnection must be exponential backoff (not fixed interval).
