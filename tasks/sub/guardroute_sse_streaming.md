# Subtask: GuardRoute SSE Streaming

**Parent Link:** `base/guardroute_orchestrator.md`

## Actionable Steps
- [x] Stream final LLM completion token-by-token to frontend via SSE.
- [x] Endpoint: `POST /api/guardroute/chat` returning `text/event-stream`.
- [x] Emit metadata header block (complexity, agents, latency, model used) before streaming tokens.

## Definition of Done
- SSE endpoint functions and streams tokens after sending the diagnostics metadata header.
