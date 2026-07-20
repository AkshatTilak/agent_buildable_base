# Subtask: Agent CRUD API Endpoints

**Parent Link:** `base/04_agent_management_crud.md`

## Actionable Steps
- [ ] Create router `/api/agents` in the Gateway.
- [ ] Implement the CRUD API endpoints:
  - [ ] `GET /api/agents`: Retrieve a list of all defined agents.
  - [ ] `GET /api/agents/{id}`: Fetch details for a specific agent.
  - [ ] `POST /api/agents`: Create a new agent (validate prompt, model_id, and tool configurations).
  - [ ] `PUT /api/agents/{id}`: Update agent configurations.
  - [ ] `DELETE /api/agents/{id}`: Remove an agent definition.
- [ ] Connect the models dropdown dynamically to list models available inside `ModelRegistryModel`.

## Dependencies
- `sub/agent_crud_schema.md`

## Definition of Done
- CRUD API endpoints fully functional and returning appropriate REST status codes (e.g. 201 Created, 404 Not Found).
- Swagger docs `/docs` display standard CRUD schemas.
