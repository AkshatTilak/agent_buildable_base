# Subtask: Agent Hub UI

**Parent Link:** `base/04_agent_management_crud.md`

## Actionable Steps
- [ ] Build the grid layout component: `frontend/src/components/AgentHub.tsx`.
- [ ] Implement card views for each agent, displaying agent names, assigned models, and active tools.
- [ ] Create a dialog form modal to Create/Edit an agent:
  - [ ] Text inputs for Name, Role description.
  - [ ] Textarea for System Prompt.
  - [ ] Dropdown select for LLM Model ID (populated from `/api/models`).
  - [ ] Checklist/badges for available tools (web search, retrieval search, sandbox coding).
  - [ ] Slider inputs for temperature.
- [ ] Wire save/delete buttons to the REST API endpoints and update Zustand stores upon completion.

## Dependencies
- `sub/frontend_api_integration.md`
- `sub/agent_crud_api.md`

## Definition of Done
- Users can view, create, edit, and delete agents from the Web UI.
- Inputs are validated on the UI side to prevent empty prompt or name submission.
