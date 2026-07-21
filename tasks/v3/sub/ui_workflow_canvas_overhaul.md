# Subtask: WorkflowCanvas Premium UI Overhaul

**Parent Link:** `base/03_frontend_premium_ui.md`

## Actionable Steps
- [x] Add ReactFlow `MiniMap` component for large workflow overview.
- [x] Add a draggable node palette sidebar (left side) with node type templates users can drag onto canvas.
- [x] Add workflow list dropdown in the toolbar — load previously saved workflows from API.
- [x] Add "Export JSON" button to download current workflow graph as a JSON file.
- [x] Add edge labels for conditional routing indicators.
- [x] Improve custom node styling: add gradient top borders matching node type color, type icon in header.
- [x] Add node status indicators: idle (gray dot), running (emerald pulse), error (rose dot).
- [x] Add undo/redo keyboard shortcuts (Ctrl+Z / Ctrl+Shift+Z) with state history stack.

## Dependencies
- `sub/design_css_tokens.md`.

## Definition of Done
- MiniMap visible in bottom-right of canvas.
- Node palette sidebar allows drag-to-create.
- Workflow list dropdown loads saved workflows.
- Export JSON downloads file to user's machine.
