# Frontend Design System & Component Pattern Guide (V3)

> **Source:** ContAIned V3 Design Language Specification
> **Last Updated:** 2026-07-21
> **Authority:** All frontend changes MUST comply with this design system. Deviation requires explicit approval.

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
├── components/
│   ├── layout/           // Sidebar, Header, Breadcrumbs, CommandPalette
│   ├── dashboard/        // SystemMetrics, ServiceHealth, ModelRegistry
│   ├── ingestion/        // IngestionPanel, UploadZone, JobTracker
│   ├── workflow/          // WorkflowCanvas, PropertyDrawer, nodes/
│   ├── agents/           // AgentHub, AgentCard, AgentModal
│   ├── evalops/          // EvalPanel, TestCaseTable, ScoreChart
│   └── shared/           // LoadingSkeleton, Toast, StatusBadge, ConfirmModal
├── store/                // Zustand slices (one per domain)
├── services/             // API client, telemetry, localStorage helpers
├── hooks/                // Custom React hooks (useKeyboard, useDebounce)
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

- All interactive elements must have `aria-label` or visible text.
- All images/icons in interactive context must have `title` attribute.
- Focus states must be visible (indigo focus ring).
- `tabIndex` must be set correctly on custom interactive elements.
- No layout shifts on data load (use skeletons that match final dimensions).
- All lists > 50 items must use virtualization (`react-window` or similar).
- WebSocket reconnection must be exponential backoff (not fixed interval).
