# Subtask: Agent Hub UI

**Parent Link:** `base/04_agent_management_crud.md`

## Actionable Steps
- [x] Build the grid layout component: `frontend/src/components/AgentHub.tsx`.
- [x] Implement card views for each agent, displaying agent names, assigned models, and active tools.
- [x] Create a dialog form modal to Create/Edit an agent:
  - [x] Text inputs for Name, Role description.
  - [x] Textarea for System Prompt.
  - [x] Dropdown select for LLM Model ID (populated from `/api/models`).
  - [x] Checklist/badges for available tools (web search, retrieval search, sandbox coding).
  - [x] Slider inputs for temperature.
- [x] Wire save/delete buttons to the REST API endpoints and update Zustand stores upon completion.

## Dependencies
- `sub/frontend_api_integration.md`
- `sub/agent_crud_api.md`

## Definition of Done
- Users can view, create, edit, and delete agents from the Web UI.
- Inputs are validated on the UI side to prevent empty prompt or name submission.
