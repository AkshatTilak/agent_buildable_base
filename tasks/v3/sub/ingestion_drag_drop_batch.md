# Subtask: Ingestion Drag-and-Drop & Batch Upload

**Parent Link:** `base/06_ingestion_documents_ux.md`

## Actionable Steps
- [x] In `IngestionPanel.tsx`, add proper drag-and-drop event handlers: `onDragOver`, `onDragLeave`, `onDrop`.
- [x] Add visual drag state — dropzone border turns emerald, overlay backdrop appears when files are dragged over.
- [x] Add batch upload support — allow selecting multiple files (`multiple` attribute on file input).
- [x] Render file queue list before upload:
  - File icon based on category (PDF, TXT, MD, Video, Image)
  - File name, size in human-readable units (KB/MB)
  - Remove file button (X) before committing upload
- [x] Add overall upload progress bar with percentage during upload request.
- [x] Add file size check — warn if file exceeds `MAX_UPLOAD_SIZE` (default 500MB).
- [x] Add toast notification upon upload completion or failure.

## Dependencies
- `sub/design_css_tokens.md`, `sub/design_shared_components.md` (for Toast, StatusBadge).

## Definition of Done
- Dragging files over the dropzone triggers visual highlight.
- Dropping multiple files adds them to an upload queue.
- Upload progress bar updates during upload.
- Batch upload completes successfully.
