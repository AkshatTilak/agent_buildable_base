# Subtask: SystemMetrics Premium UI Overhaul

**Parent Link:** `base/03_frontend_premium_ui.md`

## Actionable Steps
- [x] Add sparkline mini-charts (recharts `Sparkline` or `LineChart` with minimal config) next to each metric card showing last 60 data points of CPU/Memory/VRAM history.
- [x] Replace flat progress bars with animated SVG ring/gauge charts for VRAM utilization.
- [x] Add animated count-up effect on metric numbers when values change (use `framer-motion` `animate` or CSS counter).
- [x] Add color-coded threshold warnings: text turns amber when > 70%, rose when > 90%.
- [x] Add "last updated" relative timestamp (e.g., "2s ago") below each metric.
- [x] Add "Refresh" button in the section header to manually re-fetch health data.
- [x] Make service health cards expandable — click to see ping latency (requires backend health endpoint to report latency).
- [x] Add loading skeleton state when `systemHealth === null`.

## Dependencies
- `sub/design_css_tokens.md`, `sub/state_telemetry_settings_sync.md` (for sparkline history buffer).

## Definition of Done
- Sparkline charts render with live telemetry history.
- VRAM shows as animated gauge ring.
- Metric numbers animate on value change.
- Threshold warnings visible at 70%/90%.
