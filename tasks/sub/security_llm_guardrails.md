# Subtask: LLM Safety Guardrails

**Parent Link:** `base/security_hardening.md`

## Actionable Steps
- [x] Implement prompt injection detection: scan for known patterns, block system prompt overrides.
- [x] Implement input sanitization: strip HTML/script tags, enforce max prompt length.
- [x] Implement post-flight PII scrubbing: scan for emails, phones, API keys, SSNs. Redact before returning.
- [x] Implement optional toxicity check with configurable threshold (default: 0.1).
- [x] Implement hallucination grounding check for RAG responses.
- [x] Secure MCP tools: `query_database` rejects non-prefixed tables, `query_graph` blocks write ops, all use parameterized queries.

## Dependencies
- GuardRoute pipeline. MCP tool implementations.

## Definition of Done
- Injection attempts blocked. PII scrubbed from output. MCP tools secure.
