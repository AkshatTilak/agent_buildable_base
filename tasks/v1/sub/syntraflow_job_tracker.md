# Subtask: SyntraFlow Ingestion Job Status Tracker

**Parent Link:** `base/syntraflow_rag.md`

## Actionable Steps
- [x] Maintain a `syntraflow_jobs` table.
- [x] Publish job to Kafka topic `syntraflow-ingestion-jobs` for async processing.
- [x] Expose `GET /api/syntraflow/jobs/{job_id}` status endpoint.

## Definition of Done
- Ingestion jobs queue and expose progress/status endpoints correctly.
