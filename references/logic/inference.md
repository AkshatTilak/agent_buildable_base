# Inference Server & Model Architecture

> **Source:** Migrated from `requirements/inference.md`
> **Last Updated:** 2026-07-14

The Inference Server runs GPU-bound deep learning workloads behind the API Gateway and coordinates VRAM allocations to prevent OOM crashes. Models are selected from the configurable Model Registry (see `references/logic/model_registry.md`).

---

## 1. VRAM Manager & Budgeting ✅

### On-Demand Lazy Loading
- Keep model weights unloaded until a request targets a specific endpoint.
- Auto-load at request time, update last-used timestamp.
- Auto-download from HuggingFace Hub on first use if not cached.

### LRU Eviction Scheduler
- Configurable VRAM budget (default: 20 GB via `VRAM_BUDGET_MB`).
- If loading exceeds limit → evict Least Recently Used model.

### Idle Cleanup Daemon
- Background task checks model status every 60 seconds.
- If model inactive > `CLASSIFIER_IDLE_TIMEOUT` seconds (default: 300) → unload from VRAM.

### Device Auto-Detection
- `DEVICE=auto`: detect CUDA; fall back to CPU.
- `DEVICE=cuda`: force GPU (fail if unavailable).
- `DEVICE=cpu`: force CPU (degraded performance).

### Model Registry Integration
- On startup: read enabled local models from Registry.
- Register loaders only for `is_enabled=True` and `mode='local'` models.
- Support hot-reload on registry changes.

---

## 2. Task Classifier (Pending)
- **Default Model:** `Arch-Router-1.5B` (GGUF). VRAM: ~1-2 GB. Framework: `llama-cpp-python`.
- **Alternative:** Semantic Router (zero VRAM) — cosine-similarity via embedding model.
- **JSON Contract:**
  ```json
  {"complexity": "simple|medium|complex", "required_agents": [...], "confidence": 0.92}
  ```

---

## 3. ASR / Speech-to-Text (Pending)
- **Default Model:** SenseVoice-Small. VRAM: ~250 MB. Framework: `funasr`.
- **JSON Contract:**
  ```json
  {"text": "...", "segments": [...], "emotion": "...", "audio_events": [...], "language": "en"}
  ```
- Note: `emotion` and `audio_events` unique to SenseVoice.

---

## 4. Embedding / Joint Text & Image (Pending)
- **Default Model:** `jinaai/jina-clip-v2`. VRAM: ~1 GB. Framework: `sentence-transformers`.
- **Native output:** 1024 elements (NOT 768 — that was v1). Supports Matryoshka dimensions.
- **Cloud Alternative:** Gemini Embedding 2 (up to 3072 dim; 10M tok/min free tier).

---

## 5. OCR / Document Layout Extraction (Pending)
- **Default Local:** GLM-OCR (0.9B, ~2 GB VRAM).
- **Legacy:** PaddleOCR (~2-5 GB).
- **Cloud:** Gemini 3.5 Flash (zero local VRAM).
- **JSON Contract:**
  ```json
  {"text": "...", "blocks": [...], "tables": [...], "layout": "markdown..."}
  ```
- **Missing Dependencies** (add to inference extras): `funasr`, `paddlepaddle-gpu`/`paddleocr`, `surya-ocr`, `docling`, move `llama-cpp-python` from base to inference extras.

---

## 6. Health Endpoint ✅
```json
{
  "status": "healthy",
  "loaded_models": [{"name": "jina-clip-v2", "vram_mb": 1000, "last_used": 1720000000.0, "load_count": 5}],
  "vram_used_mb": 3250,
  "vram_budget_mb": 20000,
  "device": "cuda",
  "registered_models": ["baidu-ocr", "jina-clip-v2", "sensevoice-small", "arch-router-1.5b"]
}
```

---

## 7. Concurrency & Performance ✅

### Request Queuing
- Simultaneous model load requests: second waits for first (`asyncio.Lock`).
- Simultaneous inference on loaded model: concurrent (no lock on inference).

### Per-Model Concurrency Limits
- Optional limit on concurrent inference calls per model (e.g., max 4 embedding batches).
- Prevents GPU memory spikes from oversized batches.

### Cold-Start vs Warm Latency Tracking
- Log cold-start latency (first request requiring VRAM load) separately from warm.
- Include in health endpoint for monitoring.
