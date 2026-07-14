# Subtask: GuardRoute Context Truncation

**Parent Link:** `base/guardroute_orchestrator.md`

## Actionable Steps
- [x] Detect fallback model context window size.
- [x] Truncate context in tokens using `tiktoken` or LiteLLM token counter based on limits (32k for 70B, 8k for free tier).

## Definition of Done
- Context payload is automatically truncated to match resolved model thresholds before invocation.
