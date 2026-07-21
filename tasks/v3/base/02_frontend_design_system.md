# Base Task: Frontend Design System Establishment

## Objective
Implement the authoritative design token system defined in `references/structure/design_system.md` into the frontend codebase as CSS custom properties, utility classes, and reusable shared components.

## Business/System Value
V2's frontend uses inconsistent hardcoded hex values, ad-hoc Tailwind classes, and no standardized component patterns. Every component has its own shade of gray, its own font size, its own border radius. V3 mandates a single source of truth design system that all existing and future components must follow — ensuring the platform looks and feels premium and consistent.

## Complexity Rating
Medium (Primarily CSS and shared component work, but requires auditing all existing components for compliance).

## Subtask Registry
* `[x] sub/design_css_tokens.md` — Implement full design token system in `index.css` as CSS custom properties.
* `[ ] sub/design_shared_components.md` — Create reusable shared components (StatusBadge, LoadingSkeleton, ConfirmModal, Toast).
* `[ ] sub/design_component_audit.md` — Audit and update all existing components to use design tokens instead of hardcoded values.
