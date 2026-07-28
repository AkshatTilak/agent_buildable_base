# Model Playground Architecture

> **Source:** V5 Planning  
> **Last Updated:** 2026-07-24  
> **Status:** Implemented (S5-04a, S5-04b, S5-04c, S5-04d, S5-04e)

---

## 1. Overview

Interactive playground for chatting with any registered model. Supports multi-format file attachments (PDF, PNG/JPG/WEBP OCR, video/audio transcription preview, TXT/MD/CSV text extraction), real-time SSE token streaming responses, session persistence (`playground_sessions` table), and system prompt customization.

> **Scope (V6):** the Playground remains a **platform-level** surface at `/playground`, available to **all authenticated users**. It is *not* hub-scoped and is not reachable under `/hubs/{hub_id}/…`. See [`hubs.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/hubs.md) §7.

---

## 2. Chat Flow

```
User selects model → Types prompt → (optional: attaches files) → POST /api/playground/chat →
  → Resolve model from registry / LiteLLM fallback chain
  → If attachments: fetch extracted text, prepend as context
  → Build messages array (system_prompt + history + current prompt)
  → completion_with_fallback(model, messages, stream=True/False)
  → Stream tokens (SSE) or return JSON completion to frontend
```

---

## 3. File Processing Pipeline

| File Type | Processing | Storage / TTL |
|---|---|---|
| PDF | Text extraction via `pdfplumber`/`pypdf` | `gateway/temp_uploads/` (1-hour TTL) |
| PNG/JPG/WEBP | Image OCR scanning preview | `gateway/temp_uploads/` (1-hour TTL) |
| MP4/WEBM | Audio/video transcription preview | `gateway/temp_uploads/` (1-hour TTL) |
| TXT/MD/CSV | Direct UTF-8 text read | `gateway/temp_uploads/` (1-hour TTL) |

- Files stored temporarily in `gateway/temp_uploads/` with 1-hour TTL auto-cleanup
- Attachment metadata tracked in `ATTACHMENT_STORE` in `gateway/api/playground.py`

---

## 4. Session Persistence

- `playground_sessions` table: `id`, `user_id` (nullable FK), `hub_id` (nullable FK, V6), `name`, `model_id`, `system_prompt`, `messages_json`, `attachments_json`, `temperature`, `max_tokens`, `created_at`, `updated_at`
- Migration script: `migrations/versions/f3c4d5e6f7a8_add_playground_sessions.py`
- Auto-name: generated from first user query truncated to 80 chars if no explicit name provided
- Auto-save: triggered on each message exchange

### Hub context tag (V6)

`playground_sessions.hub_id` is **nullable** and acts purely as an optional **context tag** — sessions are user-scoped, not hub-scoped, and a session with `hub_id = NULL` is fully functional. The tag exists so a user can associate scratch work with the hub they were working in.

When a session *is* tagged with a hub, the UI may narrow its pickers to that hub's reachable resources:

- the **model selector** may be filtered to models permitted by that hub's settings,
- the **collection picker** (for retrieval-augmented prompts) may be filtered to collections in that hub and in the ingestion hubs it is linked to, resolved via `common/services/hub_resolver.py`.

This is a convenience filter only. It never grants access: an untagged session sees the same platform-level model registry every authenticated user sees, and a tagged session can never reach resources the user could not otherwise read.

---

## 5. API Endpoints

| Endpoint | Method | Description |
|---|---|---|
| `/api/playground/chat` | POST | Chat completion (supports stream=true/false) |
| `/api/playground/upload` | POST | Upload and process file attachment |
| `/api/playground/attachments/{id}` | GET | Get full extracted text for attachment |
| `/api/playground/attachments/{id}` | DELETE | Delete temp attachment and file |
| `/api/playground/sessions` | GET | List saved user sessions |
| `/api/playground/sessions` | POST | Create/save new session |
| `/api/playground/sessions/{id}` | GET | Load single session |
| `/api/playground/sessions/{id}` | PUT | Update session (auto-save) |
| `/api/playground/sessions/{id}` | DELETE | Delete session |

---

## 6. Frontend UI Components

- `PlaygroundPage.tsx`: Main page shell with model selector, system prompt drawer, parameter controls, message list with token badges, copy, edit & resend, and stream animation.
- `AttachmentZone.tsx`: File upload dropzone with previews, thumbnails, text snippets, and removal actions.
- `SessionSidebar.tsx`: Collapsible sessions panel for loading, renaming, searching, and deleting saved sessions.

