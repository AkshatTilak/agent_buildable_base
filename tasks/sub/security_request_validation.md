# Subtask: Security Request Validation

**Parent Link:** `base/security_hardening.md`

## Actionable Steps
- [x] Enforce maximum request body sizes: 100 MB for uploads, 1 MB for JSON payloads.
- [x] Validate `Content-Type` headers on upload endpoints.
- [x] Sanitize filename inputs to prevent path traversal.

## Dependencies
- FastAPI upload client, path sanitization utilities.

## Definition of Done
- Oversized requests are blocked, content types are checked, and filenames are sanitized against path traversal.
