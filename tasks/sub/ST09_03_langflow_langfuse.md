# ST09_03: Configure Langflow–Langfuse Observability

## Parent Link
`base/BT09_langflow_integration.md`

## Actionable Steps
- [ ] Step 1: Inject LANGFUSE_SECRET_KEY, LANGFUSE_PUBLIC_KEY, LANGFUSE_BASE_URL into Langflow container
- [ ] Step 2: Verify Langflow sends traces to Langfuse automatically
- [ ] Step 3: Test that visual flow executions appear in Langfuse dashboard
- [ ] Step 4: Validate trace data includes flow_id and execution metadata

## Dependencies
- `ST07_01_langfuse_deploy.md` — Langfuse must be running
- `ST09_01_langflow_deploy.md` — Langflow must be running

## Definition of Done
All Langflow flow executions are automatically traced and visible in Langfuse.
