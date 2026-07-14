# ST05_01: Implement RAGFlow SDK Client Wrapper

## Parent Link
`base/BT05_rag_integration.md`

## Actionable Steps
- [x] Step 1: Initialize RAGFlow client with api_key and base_url from settings
- [x] Step 2: Implement `create_user_dataset()` with chunking config
- [x] Step 3: Implement `upload_document()` with parsing trigger
- [x] Step 4: Implement `retrieve()` with similarity threshold
- [x] Step 5: Wrap in module-level singleton `get_ragflow_client()`

## Dependencies
- `ragflow-sdk ^0.15.0` installed in pyproject.toml ✅
- RAGFLOW_API_KEY and RAGFLOW_API_URL in settings ✅

## Implementation Notes
- `ragflow_sdk` is synchronous — callers use `asyncio.to_thread()` for async contexts
- Chunk method: "naive", token limit: 512, layout_recognize: True (DeepDoc OCR)
- Graceful error handling in `retrieve()` — returns empty list on SDK errors
- Singleton pattern via `get_ragflow_client()` function

## Completion
**Status: [x] COMPLETE** — Implemented 2026-07-14 (as part of BT04 tooling).
`../../echomind-core/app/rag/ragflow_client.py`
