# Subtask: Workflow ReactFlow Setup

**Parent Link:** `base/02_visual_workflow_builder.md`

## Actionable Steps
- [x] Install ReactFlow dependencies in `frontend/package.json` (`@xyflow/react` or standard `reactflow`).
- [x] Initialize the drag-and-drop workspace canvas in `frontend/src/components/WorkflowCanvas.tsx`.
- [x] Define custom Node components:
  - [x] `ClassifierNode`: Visual layout representing the Arch-Router complexity check.
  - [x] `AgentNode`: Visual representation of an agent with prompt override indicator and tool list.
  - [x] `RetrievalNode`: For fetching documents from hybrid storage.
  - [x] `SynthesisNode`: For compiling and gathering multiple outputs.
- [x] Configure custom Edge styling to display routing conditions or fallback status.

## Dependencies
- `sub/frontend_component_breakdown.md`

## Definition of Done
- ReactFlow canvas loads in the browser, showing custom node templates.
- Users can drag, drop, and link nodes together dynamically, with node/edge connections preserved in local ReactFlow state.
