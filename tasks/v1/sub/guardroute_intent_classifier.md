# Subtask: GuardRoute Intent & Complexity Classifier

**Parent Link:** `base/guardroute_orchestrator.md`

## Actionable Steps
- [x] Route user prompt to inference server `/infer/classify` endpoint.
- [x] Categorize into: Simple (direct to synthesis), Medium (single subagent), Complex (parallel execution).
- [x] Implement rule-based fallback routing on failure.
- [x] Implement circuit breaker: after 5 consecutive failures, bypass classifier for 60 seconds.

## Definition of Done
- Query routing classification successfully separates simple, medium, and complex inputs, with fallback behaviors enabled.
