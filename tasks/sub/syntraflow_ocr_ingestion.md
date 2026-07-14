# Subtask: SyntraFlow OCR Ingestion

**Parent Link:** `base/syntraflow_rag.md`

## Actionable Steps
- [x] Configure OCR engine (via Model Registry).
- [x] Local Mode: Load local model (GLM-OCR) into VRAM on request, extract structures, convert to Markdown.
- [x] API Mode: Pass doc images directly to cloud model (Gemini 3.5 Flash) for layout-preserving Markdown/JSON.

## Definition of Done
- Documents process through both OCR pathways producing layouts.
