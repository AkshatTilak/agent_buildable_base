# Base Task 10: Live API Tests (External Service Integration) (v8)

## Objective
Tests that require real API keys — validate LiteLLM routing, inference server proxying, model response quality, real MCP tool interactions, and Kafka messaging.

## Why
Live API tests validate the actual external integrations (Gemini, OpenRouter, MCP servers, Kafka) that mock-based tests cannot. They require real API keys from `.env.test` and are gated behind the `live_api` marker.

## Scope
- **`tests/live_api/test_litellm_routing.py`**:
  - Call primary (Gemini Flash) → verify response structure.
  - Simulate primary failure → verify fallback to OpenRouter.
  - Verify model selection respects `DEEPEVAL_MODEL` config.
- **`tests/live_api/test_inference_proxy.py`**:
  - Proxy request through gateway to inference server (if running).
  - Verify OCR endpoint with a real image.
  - Verify embedding endpoint with real text.
- **`tests/live_api/test_mcp_tools_real.py`** — Real MCP tool integration:
  - Stand up a sample MCP server from `mcp_tools/` → register via API → health check → discover tools → invoke tool → verify response.
  - Test tool discovery via REST (`/tools`), JSON-RPC (`tools/list`), and direct probe endpoints.
  - Test tool invocation via REST (`/invoke`) and JSON-RPC (`tools/call`).
  - Test encrypted auth token round-trip (register with `bearer` auth → invoke with decrypted token).
  - Test tool execution within a GuardRoute workflow node (MCP tool executor).
  - Test DB-scoped MCP tools (`/hubs/{hub_id}/db-tools`) with real external DB credential.
  - Test tool enable/disable toggle → verify disabled tools are excluded from discovery.
- **`tests/live_api/test_kafka_messaging.py`** — Kafka integration:
  - Publish ingestion job to Kafka topic → verify SyntraFlow consumer picks it up.
  - Publish eval trigger → verify EvalOps consumer processes it.
  - Test Kafka-offline fallback (stop Kafka → verify local background execution takes over).

## Associated Subtasks
1. `[ ]` `sub_10_01_litellm_routing.md`: `test_litellm_routing.py` — routing & fallback chains.
2. `[ ]` `sub_10_02_inference_proxy.md`: `test_inference_proxy.py` — OCR & embedding endpoints.
3. `[ ]` `sub_10_03_mcp_tools_real.md`: `test_mcp_tools_real.py` — real MCP tool lifecycle & workflow integration.
4. `[ ]` `sub_10_04_kafka_messaging.md`: `test_kafka_messaging.py` — Kafka publish/consume & offline fallback.

## Definition of Done
- LiteLLM routing and fallback chains verified with real API keys against actual running Gateway (`:8000`).
- Inference proxy OCR/embedding endpoints verified against actual running Inference server (`:8001`).
- Real MCP tool discovery, invocation, auth round-trip, workflow integration, and enable/disable verified.
- Kafka publish/consume verified on actual Kafka broker (`:9092`) with verified offline fallback.
- If errors arise, inspect Docker container logs (`docker compose logs kafka db`) and fix root causes directly in gateway or submodules.
- Deleting test data is not necessary.
