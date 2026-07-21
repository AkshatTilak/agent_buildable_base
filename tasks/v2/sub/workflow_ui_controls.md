# Subtask: Workflow UI Controls

**Parent Link:** `base/02_visual_workflow_builder.md`

## Actionable Steps
- [x] Build a slide-out property drawer (`frontend/src/components/PropertyDrawer.tsx`) that opens when double-clicking a Node.
- [x] Implement parameter forms in the drawer:
  - [x] For `AgentNode`: Toggle tool authorizations, edit local system prompts, select model IDs from the registry list.
  - [x] For `ClassifierNode`: Edit condition expressions or confidence thresholds.
  - [x] For `RetrievalNode`: Select retrieve limits or weights for hybrid search.
- [x] Ensure changes in the drawer immediately update the local ReactFlow node data state.

## Dependencies
- `sub/workflow_reactflow_setup.md`

## Definition of Done
- Drawer panel displays context-sensitive configurations based on the selected node type.
- Property modifications correctly sync with the parent component's ReactFlow state.
