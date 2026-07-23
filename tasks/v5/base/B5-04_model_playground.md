# B5-04: Model Playground

> **Status:** `[ ]`  
> **Owner:** `gateway`  
> **Secondary:** `projects/syntraflow`, `inference`, `frontend`  
> **Complexity:** 🟡 Medium (5 subtasks)

---

## Objective

Provide an interactive playground page where users can chat with any model from the registry, attach files (PDF, images, video), and save/load conversation sessions. The playground serves as both a testing tool and a power-user interface for direct model interaction.

---

## Acceptance Criteria

- [ ] `POST /api/playground/chat` accepts model_id, messages, system_prompt, temperature, max_tokens, attachment refs
- [ ] SSE streaming support for playground chat
- [ ] `POST /api/playground/upload` handles PDF → text extraction, images → OCR, video → ASR transcription
- [ ] Uploaded file content injected into conversation context for the model
- [ ] Playground sessions persist to DB (save/load/delete)
- [ ] Frontend has full chat UI with model selector, system prompt editor, parameter sliders
- [ ] File attachment drag-and-drop with preview (text, thumbnail, transcription)
- [ ] Token usage displayed per message
- [ ] Copy response, regenerate, edit & resend capabilities

---

## Linked Subtasks

| ID | Title | File |
|---|---|---|
| S5-04a | Playground Chat API | [`S5-04a.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-04a.md) |
| S5-04b | File Upload & Processing Pipeline | [`S5-04b.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-04b.md) |
| S5-04c | Playground Session Persistence | [`S5-04c.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-04c.md) |
| S5-04d | Frontend: PlaygroundPage Chat UI | [`S5-04d.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-04d.md) |
| S5-04e | Frontend: File Attachment Zone & Session Sidebar | [`S5-04e.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-04e.md) |
