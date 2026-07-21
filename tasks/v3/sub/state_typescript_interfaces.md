# Subtask: Create TypeScript Interfaces for All API Responses

**Parent Link:** `base/05_frontend_state_data_layer.md`

## Actionable Steps
- [ ] Create `frontend/src/types/` directory.
- [ ] Create `types/api.ts` with interfaces matching backend Pydantic schemas:
  ```typescript
  // System
  interface SystemHealthResponse { status, environment, active_projects, services, inference_details }
  interface ServiceStatus { gateway, inference_server, database, redis, neo4j, qdrant, kafka }
  
  // Models
  interface ModelRegistryEntry { model_id, display_name, role, provider, is_enabled, is_default }
  interface ModelRegistryResponse { [role: string]: { active: ModelRegistryEntry | null, available: ModelRegistryEntry[] } }
  
  // Agents
  interface AgentResponse { id, name, role, system_prompt, model_id, tools, temperature, max_tokens, created_at, updated_at }
  interface AgentCreatePayload { name, role, system_prompt, model_id, tools?, temperature?, max_tokens? }
  interface AgentUpdatePayload { name?, role?, system_prompt?, model_id?, tools?, temperature?, max_tokens? }
  
  // Workflows
  interface WorkflowResponse { id, name, graph_json, is_active, created_at, updated_at }
  interface WorkflowCreatePayload { name, graph_json }
  
  // Ingestion
  interface IngestionResponse { job_id, status, message }
  interface IngestionJobResponse { job_id, document_id, status, progress, error_msg, created_at, updated_at }
  
  // EvalOps
  interface EvalDashboardResponse { ... }
  interface EvalRunResponse { ... }
  interface TestCaseResponse { ... }
  ```
- [ ] Create `types/telemetry.ts` for WebSocket message types.
- [ ] Export all types from a barrel `types/index.ts`.

## Dependencies
- None.

## Definition of Done
- `types/` directory contains all API response interfaces.
- All interfaces match backend Pydantic schema field names.
- Barrel export file exists.
