# Subtask: Architecture Dependency Resolution

**Parent Link:** `base/06_architecture_cleanup.md`

## Actionable Steps
- [x] Profile service imports to identify circular dependencies between `gateway` and `inference`.
- [x] Decouple helper imports: ensure `gateway` never imports modules from `inference` that execute models locally.
- [x] Move any model schemas or validation helpers used by both services to `common/schemas/`.
- [x] Ensure `gateway` interacts with the `inference` server strictly via HTTP/REST protocol requests.

## Dependencies
- None

## Definition of Done
- No package imports exist from `gateway` to `inference`, or vice versa.
- Python runtime launches all services successfully without circular import errors.
