# Model Playground Architecture

> **Source:** V5 Planning  
> **Last Updated:** 2026-07-23

---

## 1. Overview

Interactive playground for chatting with any registered model. Supports file attachments (PDF, images, video), streaming responses, session persistence, and system prompt customization.

---

## 2. Chat Flow

```
User selects model → Types prompt → (optional: attaches files) → POST /api/playground/chat →
  → Resolve model from registry
  → If attachments: fetch extracted text, prepend as context
  → Build messages array (system_prompt + history + user message)
  → completion_with_fallback(model, messages, stream=True)
  → SSE stream tokens to frontend
```

---

## 3. File Processing Pipeline

| File Type | Processing | Service |
|---|---|---|
| PDF | Text extraction (pdfplumber/PyPDF2) | Gateway or SyntraFlow |
| PNG/JPG/WEBP | OCR via inference `/infer/ocr` | Inference Server |
| MP4/WEBM | Audio extraction → ASR via `/infer/transcribe` | Inference Server |
| TXT/MD/CSV | Direct text read | Gateway |

- Files stored temporarily in `gateway/temp_uploads/` with 1-hour TTL
- Attachment metadata tracked in Redis (keyed by attachment_id)
- For vision models: raw image bytes retained for multimodal input

---

## 4. Session Persistence

- `playground_sessions` table: id, user_id, name, model_id, system_prompt, messages_json, attachments_json, temperature, max_tokens, timestamps
- Auto-name: first user message truncated to 100 chars
- Auto-save: on each message exchange

---

## 5. API Endpoints

| Endpoint | Method | Description |
|---|---|---|
| `/api/playground/chat` | POST | Chat completion with model |
| `/api/playground/upload` | POST | Upload and process file |
| `/api/playground/attachments/{id}` | GET | Get full extracted text |
| `/api/playground/sessions` | GET | List saved sessions |
| `/api/playground/sessions` | POST | Save session |
| `/api/playground/sessions/{id}` | GET | Load session |
| `/api/playground/sessions/{id}` | PUT | Update session |
| `/api/playground/sessions/{id}` | DELETE | Delete session |
