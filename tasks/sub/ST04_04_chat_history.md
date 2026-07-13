# ST04_04: Implement Redis Chat History

## Parent Link
`base/BT04_agent_orchestration.md`

## Actionable Steps
- [ ] Step 1: Configure RedisChatMessageHistory with session_id-based keys
- [ ] Step 2: Implement rolling window (last 20 messages) for context management
- [ ] Step 3: Set appropriate TTL (1 hour) for chat sessions
- [ ] Step 4: Wire chat history into agent orchestrator
- [ ] Step 5: Implement rate limiter integration using token bucket algorithm

## Dependencies
- Redis container running
- `langchain-community` installed (provides RedisChatMessageHistory)

## Definition of Done
Chat history persists across messages, rolling window works, rate limiting enforced.
