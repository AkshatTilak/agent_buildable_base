# Subtask 14_03: Workflow Run SSE Tests

## Parent Base Task
`tasks/v8/base/14_websocket_sse_streaming_tests.md`

## Objective
Create `tests/streaming/test_workflow_sse.py` — workflow run SSE.

## Tasks
1. [ ] Start a workflow run → open SSE stream → verify event sequence: `run_start` → `node_start` → `node_end` → ... → `run_end`.
2. [ ] Validate each event's JSON structure (node_id, status, timestamps, output).
3. [ ] Test stream for multi-node workflows (verify correct ordering).
4. [ ] Test stream for failed workflow runs (error events).
5. [ ] Test reconnecting to a completed run's stream (should get final state or empty).
6. [ ] Test concurrent SSE connections to same run.

## Definition of Done
- Workflow run SSE tests pass with correct event ordering and JSON structure.
- Failed-run error events, reconnection, and concurrency verified.
