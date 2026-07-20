# Subtask: Architecture Error Handling Standardization

**Parent Link:** `base/06_architecture_cleanup.md`

## Actionable Steps
- [x] Define custom base exception classes in `common/observability/exceptions.py`.
- [x] Create standardized FastAPI exception handlers and mount them as middleware in the Gateway, Inference, and Project routers.
- [x] Ensure all API errors return a consistent JSON schema: `{ "error_code": "...", "message": "...", "details": {...} }`.
- [x] Set up consistent log messages and request trace-id propagation for cross-service HTTP requests.

## Dependencies
- None

## Definition of Done
- Standardized error handlers catch unexpected exceptions across all services.
- Failed REST operations return consistent JSON schemas.
