# Base Task: Ingestion & Documents UX Enhancement

## Objective
Transform the SyntraFlow ingestion experience from a basic file upload form into a full document management system with real drag-and-drop, batch uploads, file previews, job progress polling, a document library view, and SSE-based real-time job updates.

## Business/System Value
V2's ingestion panel lets you upload one file at a time with no progress tracking, no file previews, no way to see previously ingested documents, and no persistent job history. Users need to manage their knowledge base — see what's been ingested, track processing status, upload multiple files at once, and get real-time feedback on pipeline progress.

## Complexity Rating
High (Requires both frontend and backend changes — new API endpoints + complex UI components).

## Subtask Registry
* `[ ] sub/ingestion_drag_drop_batch.md` — Real drag-and-drop with `onDragOver`/`onDrop`, batch file upload, file type icons, size display.
* `[ ] sub/ingestion_job_tracker.md` — Job queue list showing all past/active jobs with status badges, progress bars, auto-polling via `/api/syntraflow/jobs/{id}`.
* `[ ] sub/ingestion_advanced_settings_redesign.md` — Redesign advanced settings into tabbed interface (Chunking | Pre-processors | Post-processors).
* `[ ] sub/ingestion_document_library.md` — New document library view: list all ingested documents with metadata, chunk counts, delete action. Requires backend `GET /documents` endpoint.
