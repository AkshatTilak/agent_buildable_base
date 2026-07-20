# Subtask: Architecture API Contracts

**Parent Link:** `base/06_architecture_cleanup.md`

## Actionable Steps
- [x] Review all cross-service REST interfaces.
- [x] Create explicit OpenAPI specs / schema validation structures for communication between the Gateway and the Inference Server.
- [x] Implement client-side wrapper methods in `common/clients/inference.py` to interact with the Inference Server.
- [x] Ensure the Gateway and other services use this client wrapper instead of making ad-hoc HTTP calls.

## Dependencies
- `sub/arch_dependency_resolution.md`

## Definition of Done
- Swagger documentation accurately reflects cross-service API schemas.
- Inference Server API integrations are fully validated via mock clients.
