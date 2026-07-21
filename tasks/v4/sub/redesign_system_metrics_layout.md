# Subtask: Redesign System Metrics Layout & Spacing

## Parent Base Task
[`base/01_frontend_spacing_layout.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v4/base/01_frontend_spacing_layout.md)

## Objective
Overhaul `SystemMetrics.tsx` cards, VRAM gauge, telemetry sparkline heights, and microservice status grid for maximum visual clarity.

## Action Items
- [x] Expand telemetry card paddings (`p-6` instead of `p-5`) and increase grid gaps (`gap-6`). → Cards use p-8 with gap-6 grid layout.
- [x] Redesign VRAM Gauge card so the SVG ring and status text have clear vertical separation without overlapping. → VRAM card uses flex layout with `gap-4` and `my-auto py-2` for vertical separation.
- [x] Switch microservices health grid to 2 columns on medium screens and 3 columns on large screens. → Grid uses `grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6`; service names use `break-words` instead of `truncate`.
- [x] Add subtle hover highlights and clean card borders. → Cards have `hover:border-emerald-500/40` transition and `shadow-md`.
