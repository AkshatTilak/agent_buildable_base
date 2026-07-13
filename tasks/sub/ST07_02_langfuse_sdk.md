# ST07_02: Initialize Langfuse SDK

## Parent Link
`base/BT07_observability.md`

## Actionable Steps
- [ ] Step 1: Implement `init_langfuse()` with API keys from config
- [ ] Step 2: Wire initialization into FastAPI lifespan startup
- [ ] Step 3: Verify basic trace creation from Python SDK
- [ ] Step 4: Test that traces appear in the Langfuse web UI

## Dependencies
- `ST07_01_langfuse_deploy.md` — Langfuse must be running with API keys
- `langfuse` Python package installed

## Definition of Done
Langfuse client initialized at startup, basic traces visible in web UI.
