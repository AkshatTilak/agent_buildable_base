# Subtask 14_06: External API SSE Tests

## Parent Base Task
`tasks/v8/base/14_websocket_sse_streaming_tests.md`

## Objective
Create `tests/streaming/test_external_api_sse.py` — external API SSE.

## Tasks
1. [ ] Call external endpoint with API key auth + `stream: true` → validate SSE chunks.
2. [ ] Verify OpenAI-compatible response format in stream mode.
3. [ ] Verify usage stats in final chunk.
4. [ ] Test fallback chain behavior during streaming (primary fails → secondary picks up).

## Definition of Done
- External API SSE tests pass with OpenAI-compatible format and usage stats in final chunk.
- Fallback chain behavior during streaming verified.
