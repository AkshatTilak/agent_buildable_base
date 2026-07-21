# Base Task: Frontend Routing & Navigation System

## Objective
Replace the tab-based navigation system with proper React Router routing, add animated page transitions via framer-motion, implement a command palette (⌘K / Ctrl+K), and add breadcrumbs for navigation context.

## Business/System Value
V2 uses a simple `activeTab` state to swap between panels — no URL routes, no browser back/forward support, no deep linking, no keyboard navigation shortcuts. A production dashboard must have proper URL routing so users can bookmark `/agents` or share a link to `/ingestion`. The command palette provides power-user efficiency for common actions.

## Complexity Rating
Medium (Routing is well-understood, but requires updating every navigation touchpoint).

## Subtask Registry
* `[ ] sub/routing_react_router_setup.md` — Install react-router-dom, define routes, wrap App in BrowserRouter.
* `[ ] sub/routing_page_transitions.md` — Install framer-motion, add AnimatePresence with fade+slide transitions.
* `[ ] sub/routing_command_palette.md` — Build CommandPalette component with fuzzy search and keyboard navigation.
* `[ ] sub/routing_breadcrumbs_header.md` — Add top header bar with dynamic breadcrumbs and global status indicators.
