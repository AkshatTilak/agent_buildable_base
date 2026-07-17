# Subtask: SyntraFlow Duplicate Detection

**Parent Link:** `base/syntraflow_rag.md`

## Actionable Steps
- [x] Compute SHA-256 hash of file content on upload.
- [x] Check against existing hashes in `syntraflow_documents` table.
- [x] If duplicate: return existing doc ID; skip re-processing.

## Definition of Done
- Duplicate uploads are caught immediately and skipped.
