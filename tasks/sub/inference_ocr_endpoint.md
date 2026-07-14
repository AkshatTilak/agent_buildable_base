# Subtask: Inference OCR Endpoint

**Parent Link:** `base/inference_models.md`

## Actionable Steps
- [ ] Implement `/infer/ocr` endpoint on inference server.
- [ ] Default local model: GLM-OCR (0.9B params, ~2 GB VRAM). Framework: `transformers`.
- [ ] Legacy option: PaddleOCR (~2-5 GB VRAM).
- [ ] Cloud option: Gemini 3.5 Flash (vision mode, zero local VRAM).
- [ ] Input: PDF page or document image bytes.
- [ ] Output: `{"text": "...", "blocks": [...], "tables": [...], "layout": "markdown..."}`.
- [ ] Register with VRAM Manager lazy loading system.
- [ ] Add missing deps to inference extras: `paddlepaddle-gpu`, `paddleocr`, `surya-ocr`, `docling`.
- [ ] Move `llama-cpp-python` from base deps to inference extras.

## Dependencies
- Framework-specific packages per model.
- VRAM Manager, Model Registry.

## Definition of Done
- Endpoint extracts text, blocks, tables, and layout-preserving markdown from documents.
- Multiple OCR backends switchable via `OCR_MODEL` env var.
