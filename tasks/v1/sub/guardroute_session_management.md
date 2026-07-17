# Subtask: GuardRoute Session Management

**Parent Link:** `base/guardroute_orchestrator.md`

## Actionable Steps
- [x] Store active conversation sessions in Redis.
- [x] Persist completed sessions in PostgreSQL `guardroute_sessions`.
- [x] Maintain sliding history window (default: 20 turns) and trigger auto-summarization on overflow.
- [x] Inactive sessions expire after 30 minutes. Support explicit reset API.

## Definition of Done
- Conversation state is preserved in Redis cache and persists to SQL on termination/completion.
