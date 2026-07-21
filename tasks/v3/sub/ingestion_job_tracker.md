# Subtask: Ingestion Job Tracker & Polling

**Parent Link:** `base/06_ingestion_documents_ux.md`

## Actionable Steps
- [ ] Create `frontend/src/components/ingestion/JobTracker.tsx`.
- [ ] Render a list of active and recent ingestion jobs.
- [ ] Display job details: `job_id`, document name, status badge (`queued` / `processing` / `completed` / `failed`), progress percentage, creation timestamp.
- [ ] Add auto-polling logic: when there are jobs with status `queued` or `processing`, poll `GET /api/syntraflow/jobs/{id}` every 2 seconds.
- [ ] Stop polling once job transitions to `completed` or `failed`.
- [ ] Add job error details expansion — click on a failed job to see error traceback/message.
- [ ] Add filter controls to job tracker list (All / Processing / Completed / Failed).

## Dependencies
- `sub/fix_backend_documents_jobs_api.md` (for jobs endpoints).

## Definition of Done
- Active jobs show real-time progress bars.
- Auto-polling keeps job status up-to-date without page refresh.
- Error messages can be expanded for failed jobs.
