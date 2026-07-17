# Subtask: GuardRoute Async Logging & Traces

**Parent Link:** `base/guardroute_orchestrator.md`

## Actionable Steps
- [x] Construct trace payload: prompt, classification details, subagent run times, total latency, models, token counts.
- [x] Publish payload asynchronously to Kafka topic `guardroute-traces`.
- [x] Fallback to local files if Kafka broker is offline.
- [x] Track cumulative tokens per session and save to Postgres `guardroute_usage`.

## Definition of Done
- Execution traces log to Kafka asynchronously, with automatic local file fallback.
