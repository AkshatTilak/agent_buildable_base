# Resolved Bugs & Blockers: SyntraFlow Real Integration Tests (B8-05)

## Summary
During implementation of the B8-05 real-world integration test suite
(`tests/integration/syntraflow/test_ingestion_real.py`,
`test_retrieval_real.py`, `test_collections_real.py`), several existing code
defects were exposed by hitting the real Postgres/Qdrant-backed Gateway ASGI
app. All were minimal, backward-compatible fixes. Two subtask requirements
(API embedder path, local OCR path) remain blocked by infrastructure
limitations documented below.

## Fixes Applied

### 1. `httpx.RequestNotRead` on multipart uploads in test transport
- **File:** `tests/conftest.py` (`TracingASGITransport.handle_async_request`)
- **Symptom:** `test_ingestion_pdf_text_extraction_real` failed with
  `httpx.RequestNotRead: Attempted to access streaming request content,
  without having called read()`.
- **Root cause:** The transport measured request body size via
  `len(request.content)`. For multipart `files=` uploads the body is a
  streaming generator, so accessing `request.content` raises
  `httpx.RequestNotRead`.
- **Fix:** Wrapped the size measurement in try/except and called
  `await request.aread()` before re-reading content for streaming bodies.
- **Verification:** PDF upload test passes.

### 2. `SyntraFlowChunk` attribute name mismatch
- **File:** `tests/integration/syntraflow/test_ingestion_real.py`
- **Symptom:** `AttributeError: 'SyntraFlowChunk' object has no attribute
  'chunk_text'`.
- **Root cause:** The test referenced `c.chunk_text`, but the model column is
  `text` (`projects/syntraflow/src/database/models.py`). Also asserted
  `c.collection_id`, which does not exist on `SyntraFlowChunk` (only
  `hub_id` via `HubScopedMixin`; collection is reached via the document).
- **Fix:** Changed `c.chunk_text` → `c.text` and removed the invalid
  `c.collection_id` assertion.
- **Verification:** Text ingestion test passes.

### 3. `SearchHit.document_id` always `None` in search response
- **File:** `gateway/api/ingestion_hub.py` (`search_hub`)
- **Symptom:** `test_retrieval_metadata_filtering` failed:
  `assert res.get("document_id") == doc_a_id` → `None`.
- **Root cause:** `RetrievalEngine` returns `document_id` nested inside
  `hit["metadata"]["document_id"]`, but `search_hub` read it from the top
  level (`h.get("document_id")`), which is always `None`.
- **Fix:** `document_id=h.get("document_id") or (h.get("metadata") or {}).get("document_id")`.
- **Verification:** Metadata filtering test passes.

### 4. Cross-hub search returned 500 instead of 404
- **File:** `gateway/api/ingestion_hub.py` (`search_hub`)
- **Symptom:** `test_hub_scoped_retrieval_isolation` failed:
  `assert 500 in (404, 403, 422)`.
- **Root cause:** `RetrievalEngine.resolve_targets` raises
  `HTTPException(404, "Collection not found")` for foreign collections, but
  `search_hub` only caught `ValueError` and generic `Exception`, so the
  `HTTPException` collapsed into a generic 500.
- **Fix:** Added `except HTTPException: raise` before the `ValueError` and
  generic handlers so FastAPI returns the intended status code.
- **Verification:** Hub isolation test passes.

## Blockers (not testable in this environment)

### A. API embedder path (`gemini/gemini-embedding-2`)
- **Requirement:** Subtask `05_01` item 7 — ingest with
  `gemini/gemini-embedding-2` via LiteLLM and verify vectors stored.
- **Blocker:** The inference server's `/infer/embed` route
  (`inference/routes/embed.py`) resolves the model through
  `VRAMManager.ensure_loaded()`, which only has loaders for **local** models.
  There is no loader registered for `gemini/gemini-embedding-2`, so the call
  raises `ValueError: No loader registered for model: gemini/gemini-embedding-2`.
  The ingestion pipeline (`projects/syntraflow/src/ingestion/pipeline.py`)
  routes ALL embedders through `inference_client.embed()`, so API embedders
  fall back to zero-vectors.
- **Impact:** The API embedder path cannot be verified end-to-end until the
  inference server routes API embedding models through LiteLLM (or the
  pipeline calls LiteLLM directly for API embedders).

### B. Local OCR path (`OCR_PROVIDER=local`, Baidu Unlimited-OCR)
- **Requirement:** Subtask `05_01` item 8 — ingest a scanned PDF with
  `OCR_PROVIDER=local` and verify text extracted.
- **Blocker:** The OCR model (`baidu-ocr`, ~5000 MB VRAM) is not configured
  in the test DB `model_registry` (0 rows for `ocr` role) and the machine is
  CPU-only (`gpu_available: false`). The OCR path also requires a completion
  model (Gemini) to structure OCR output, which needs an external API key.
  The existing `test_ingestion_pdf_text_extraction_real` covers the direct
  PDF text-extraction path (the primary ingestion flow) but not the scanned
  image OCR path.
- **Impact:** The local OCR path cannot be verified end-to-end in this
  environment.

## Verification Command
```powershell
poetry run pytest tests/integration/syntraflow -v
```
Result: **12 passed** (3 collections + 4 ingestion + 5 retrieval). No
regressions in the full `tests/integration/syntraflow/` suite.

## Scope Rationale
The fixes are strictly the minimum changes required to make the real-world
SyntraFlow tests pass against real Postgres/Qdrant. The two blockers are
documented as infrastructure limitations rather than forcing flaky or
always-skipped tests.
