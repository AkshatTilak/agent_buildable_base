# Subtask: OCR Model Contracts & Adapters

**Parent Link:** `base/model_hub.md`

## Actionable Steps
- [ ] Implement unified OCR adapter interface that normalizes output across all OCR models.
- [ ] Support local models: GLM-OCR, Surya OCR 2, PaddleOCR-VL, GOT-OCR 2.0, Docling.
- [ ] Support cloud models: Gemini 3.5 Flash (vision), Mistral Pixtral.
- [ ] All adapters produce the standardized JSON schema:
  ```json
  {"text": "...", "blocks": [...], "tables": [...], "layout": "markdown..."}
  ```
- [ ] Register adapters with the Model Registry loader system.

## Dependencies
- Model Registry populated with OCR model entries.
- Framework-specific packages: `transformers`, `paddleocr`, `surya-ocr`, `docling`, `litellm`.

## Definition of Done
- Each OCR model can be selected via `OCR_MODEL` env var and produces standardized output.
- Cloud and local modes work interchangeably.
