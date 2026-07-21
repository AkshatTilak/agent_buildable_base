# Base Task: Frontend Premium UI Overhaul

## Objective
Transform all existing frontend panels (SystemMetrics, IngestionPanel, WorkflowCanvas, AgentHub, EvalPanel) from functional-but-basic into premium, visually impressive dashboard components with glassmorphism depth, micro-animations, sparkline charts, and animated data visualizations.

## Business/System Value
V2 components are flat cards with static text and basic progress bars. A control plane for an AI platform must visually wow the user — animated gauge rings for VRAM, sparkline mini-charts for CPU/memory history, count-up number animations on metrics, agent card hover effects, and eval score trend visualizations. This is the difference between a prototype and a product.

## Complexity Rating
High (Every component gets significant visual enhancement. Requires `framer-motion` and advanced `recharts` usage).

## Subtask Registry
* `[ ] sub/ui_system_metrics_overhaul.md` — Sparkline charts, animated gauges, threshold warnings, expandable service cards.
* `[ ] sub/ui_agent_hub_overhaul.md` — Search/filter, agent status badges, analytics preview, card hover animations, empty state.
* `[ ] sub/ui_workflow_canvas_overhaul.md` — Node palette sidebar, minimap, undo/redo, workflow list, improved node styling.
* `[ ] sub/ui_eval_panel_overhaul.md` — Run history list, per-test-case badges, multi-metric chart, CSV export, bulk import.
* `[ ] sub/ui_sidebar_overhaul.md` — Collapsible sidebar, animated active indicator, quick actions, keyboard hints.
