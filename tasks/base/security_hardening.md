# Base Task: Security Hardening

## Objective
Implement the remaining security measures across all platform layers: secret management, PII handling, database security, LLM safety guardrails (pre/post-flight), MCP tool security, network isolation, and audit/compliance logging.

## Business/System Value
Security gaps leave the platform vulnerable to data leaks (PII in logs/responses), injection attacks (prompt injection, SQL/Cypher injection), and unauthorized access. These hardening tasks bring the platform from development-safe to production-ready.

## Subtask Registry
- [x] `sub/security_auth_middleware.md`
- [x] `sub/security_rate_limiting.md`
- [x] `sub/security_cors.md`
- [x] `sub/security_request_validation.md`
- [x] `sub/security_sandbox_implementation.md`
- [x] `sub/security_sandbox_upgrade_path.md`
- [x] `sub/security_sandbox_output_capture.md`
- [x] `sub/security_secret_management.md`
- [x] `sub/security_pii_handling.md`
- [ ] `sub/security_database.md`
- [x] `sub/security_llm_guardrails.md`
- [ ] `sub/security_network.md`
- [ ] `sub/security_audit_logging.md`

## Complexity Rating
**Medium** — Individual items are straightforward but spread across many layers (gateway, clients, LLM pipeline, Docker networking).

## References
- `references/logic/security.md` — Full security policies and specs.
- `references/logic/guardroute.md` — Pre/post-flight guardrail integration.
