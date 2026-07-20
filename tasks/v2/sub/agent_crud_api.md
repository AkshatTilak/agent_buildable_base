# Subtask: Agent CRUD API Endpoints

**Parent Link:** `base/04_agent_management_crud.md`

## Actionable Steps
- [x] Create router `/api/agents` in the Gateway.
- [x] Implement the CRUD API endpoints:
  - [x] `GET /api/agents`: Retrieve a list of all defined agents.
  - [x] `GET /api/agents/{id}`: Fetch details for a specific agent.
  - [x] `POST /api/agents`: Create a new agent (validate prompt, model_id, and tool configurations).
  - [x] `PUT /api/agents/{id}`: Update agent configurations.
  - [x] `DELETE /api/agents/{id}`: Remove an agent definition.
- [x] Connect the models dropdown dynamically to list models available inside `ModelRegistryModel`.

## Dependencies
- `sub/agent_crud_schema.md`

## Definition of Done
- CRUD API endpoints fully functional and returning appropriate REST status codes (e.g. 201 Created, 404 Not Found).
- Swagger docs `/docs` display standard CRUD schemas.
