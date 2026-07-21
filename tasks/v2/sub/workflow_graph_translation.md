# Subtask: Workflow Graph Translation

**Parent Link:** `base/02_visual_workflow_builder.md`

## Actionable Steps
- [x] Define the JSON schema representation of a ReactFlow graph (nodes, edges, node-level variables/prompts).
- [x] Implement a translation parser in Python: `projects/guardroute/src/core/graph_parser.py`.
- [x] The parser must load ReactFlow's JSON configuration and dynamically build a LangGraph `StateGraph` object:
  - [x] Add nodes based on the JSON nodes list.
  - [x] Configure execution chains or tools inside each node.
  - [x] Establish edges and conditional routing rules parsed from visual links.
- [x] Ensure compiled graphs conform to safety constraints (e.g. no infinite cycles, presence of a single terminal `gather`/synthesis node).

## Dependencies
- `sub/workflow_reactflow_setup.md`

## Definition of Done
- A Python utility parses a mock ReactFlow JSON layout and successfully compiles a functional, executable LangGraph `StateGraph`.
- Automated test validates routing correctness under the parsed configuration.
