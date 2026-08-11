# Subtask 10_02: Frontend Ingestion Configuration Modal & Collection Profile UX

## Objective
Create an advanced, document-type-aware Ingestion Configuration UI in the React frontend (`frontend/src/components/IngestionHubPage.tsx`) that dynamically renders relevant pipeline step selectors based on the selected file/document type, supporting single-choice dropdowns for primary engines and multi-selectable checkboxes for post-processing enrichments.

## Tasks
1. `[x]` Build `IngestionUploadModal` component in `frontend/src/components/hubs/ingestion/IngestionUploadModal.tsx` featuring file drag-and-drop and an "Advanced Processing Options" section.
2. `[x]` Implement document-type-dependent options rendering:
   - **Documents (PDF/Docx/Image)**: OCR Engine selection (Direct Text, Local GLM-OCR, Baidu OCR, Gemini VLM), Chunking Strategy & Sliders, Embedding Model.
   - **Audio/Video**: Speech-to-Text Model (SenseVoice-Small, Whisper), Keyframe SSIM Sampling Sensitivity, Embedding Model.
   - **Plain Text**: Chunking Strategy & Sliders, Embedding Model.
3. `[x]` Add multi-selectable post-processing checkboxes for enrichment features: Summary Generation (with LLM model selector), Keyphrase Extraction, Table Structure Preservation, and Knowledge Graph Extraction (with LLM model selector).
4. `[x]` Update `CollectionsWorkspace.tsx` and collection detail views to display default ingestion pipeline profiles and allow updating per-collection default ingestion settings.
5. `[x]` Verify UI flow with end-to-end file uploads, displaying job progress and active pipeline step status in `JobsWorkspace.tsx`.
