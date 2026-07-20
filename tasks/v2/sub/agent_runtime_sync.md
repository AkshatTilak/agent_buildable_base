# Subtask: Agent Runtime Sync

**Parent Link:** `base/04_agent_management_crud.md`

## Actionable Steps
- [ ] Implement Redis Pub/Sub in `common/clients/redis.py` to support real-time message broadcasting.
- [ ] When an agent configuration is saved/updated via the API, publish a sync event to the Redis channel `agent-config-updates` containing the updated `AgentID`.
- [ ] In GuardRoute and the Inference Server, subscribe to `agent-config-updates`.
- [ ] Implement callback handlers in the running orchestrator to dynamically reload the updated system prompts or tools from the database without restarting the services.

## Dependencies
- `sub/agent_crud_api.md`

## Definition of Done
- Cache reload events are broadcast to Redis.
- Orchestrator updates system prompts on-the-fly, verified by editing a mock agent during active chat and observing prompt modifications in subsequent outputs.
