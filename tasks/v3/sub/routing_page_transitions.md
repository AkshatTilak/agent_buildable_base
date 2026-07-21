# Subtask: Page Transitions with Framer Motion

**Parent Link:** `base/04_frontend_routing_navigation.md`

## Actionable Steps
- [x] Install `framer-motion` via `npm install framer-motion`.
- [x] Create a `PageTransition.tsx` wrapper component using `motion.div` with `AnimatePresence`.
- [x] Animation spec: fade-in with slight Y translate (0→10px, opacity 0→1), duration `--transition-slow` (0.4s).
- [x] Wrap each route's component in `PageTransition`.
- [x] Ensure `AnimatePresence` uses `mode="wait"` so exit completes before enter starts.
- [x] Add `layout` prop on card grids for smooth layout animations when items are added/removed.

## Dependencies
- `sub/routing_react_router_setup.md`.

## Definition of Done
- Page transitions animate smoothly when navigating between routes.
- No layout shifts or janky transitions.
- Exit animation completes before entry starts.
