# Subtask 08_04: Hub Linking Cross-Access E2E Test

## Parent Base Task
`tasks/v8/base/08_end_to_end_workflow_tests.md`

## Objective
Create `tests/e2e/flows/test_hub_linking_cross_access.py` — cross-hub access.

## Tasks
1. [ ] Create ingestion hub → ingest documents (embed with `gemini/gemini-embedding-2` API path).
2. [ ] Create agent hub → create agent with `gemini/gemma-3-27b-it`.
3. [ ] Link agent hub ↔ ingestion hub.
4. [ ] Agent retrieves documents from linked ingestion hub (cross-hub retrieval).
5. [ ] Revoke link → verify agent can no longer access ingestion data.

## Definition of Done
- Cross-hub retrieval verified via linked hubs.
- Link revocation denies access to ingestion data.
