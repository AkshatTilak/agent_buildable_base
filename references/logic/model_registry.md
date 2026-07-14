# Model Selection Registry & Provider Configuration

> **Source:** Migrated from `requirements/models.md`
> **Last Updated:** 2026-07-14

This document defines the configurable model selection system. All model references across the platform are resolved through this registry rather than hardcoded values.

---

## 1. Model Registry Architecture ✅

### Registry Storage
PostgreSQL table `model_registry`:
```sql
CREATE TABLE model_registry (
    id              SERIAL PRIMARY KEY,
    role            VARCHAR(50) NOT NULL,    -- 'ocr', 'asr', 'embedding', 'classifier', 'completion'
    mode            VARCHAR(10) NOT NULL,    -- 'local' or 'cloud'
    provider        VARCHAR(50) NOT NULL,    -- 'huggingface', 'gemini', 'openrouter', etc.
    model_id        VARCHAR(200) NOT NULL,   -- HF repo ID, API model string, or local path
    display_name    VARCHAR(100) NOT NULL,
    framework       VARCHAR(50),             -- 'transformers', 'llama-cpp', 'litellm', etc.
    vram_mb         INT,                     -- estimated VRAM for local models; NULL for cloud
    vector_dim      INT,                     -- for embedding models; NULL otherwise
    context_window  INT,                     -- for completion/classifier models
    quantization    VARCHAR(20),             -- 'fp16', 'int8', 'q4_k_m', 'gguf', NULL
    is_default      BOOLEAN DEFAULT FALSE,
    is_enabled      BOOLEAN DEFAULT TRUE,
    priority        INT DEFAULT 0,           -- fallback ordering (lower = higher priority)
    metadata_json   JSONB,                   -- extra config
    created_at      TIMESTAMP DEFAULT NOW()
);
```
- Seeded with all supported model entries on first startup.
- Uses `model_registry_` prefix for namespace isolation.

### Environment Override Pattern
```env
OCR_MODEL=gemini          # shorthand for default 'cloud' OCR model
ASR_MODEL=sensevoice      # shorthand for default 'local' ASR model
EMBEDDING_MODEL=jina      # or 'gemini' for cloud
CLASSIFIER_MODEL=arch     # or 'semantic' for embedding-based routing
COMPLETION_MODEL=gemini   # or 'openrouter', 'groq', 'local'
```
Environment overrides take priority over database entries.

### Registry API (`common/models/registry.py`) ✅
- `get_model_spec(role, mode="auto") -> ModelSpec` — Resolve active model for a role.
- `get_active_model(role) -> ModelSpec` — Get current default model.
- `list_available(role) -> list[ModelSpec]` — List all enabled models for a role.
- `get_fallback_chain(role) -> list[ModelSpec]` — Get ordered fallback list.

### Dynamic Vector Dimension ✅
- Qdrant collection creation reads `vector_dim` from the active embedding model's registry entry.
- Switching embedding models requires re-indexing all vectors. System detects dimension mismatches and warns/blocks on startup.

---

## 2. OCR Models — Available Options

Selection criteria: **OmniDocBench** (2026) scores, VRAM usage, deployment complexity.

### Local Options

| # | Model | Params | VRAM | Framework | Key Strengths |
|---|---|---|---|---|---|
| 1 ⭐ | `THUDM/GLM-OCR` | 0.9B | ~2 GB | `transformers` | #1 on OmniDocBench; compact; excellent text/table extraction |
| 2 | Surya OCR 2 | — | ~4 GB | `surya-ocr` | 90+ language support; strong table/layout detection |
| 3 | PaddleOCR-VL 1.6B | 1.6B | ~2-5 GB | `paddlepaddle-gpu` | Production-grade; high throughput |
| 4 | `stepfun-ai/GOT-OCR2_0` | — | ~8.5 GB | `transformers` | Excellent complex table extraction; printed + handwritten |
| 5 | Docling (IBM) | — | CPU-only | `docling` | RAG-optimized; structured Markdown/JSON; no GPU needed |

### Cloud Options

| # | Model | Provider | Key Strengths |
|---|---|---|---|
| 6 ⭐ | `gemini/gemini-3.5-flash` | Google AI | Frontier-class vision; free tier 1500 RPD |
| 7 | `mistral/pixtral-large-latest` | Mistral | Strong spatial reasoning |

### OCR JSON Schema Contract
```json
{
  "text": "Extracted full text...",
  "blocks": [{"type": "paragraph|header|table|list", "content": "...", "bbox": [x1,y1,x2,y2]}],
  "tables": [{"cells": [[{"row":0,"col":0,"text":"..."}]], "bbox": [x1,y1,x2,y2]}],
  "layout": "markdown string preserving document structure"
}
```

---

## 3. ASR (Speech-to-Text) Models — Available Options

Selection criteria: **HuggingFace Open ASR Leaderboard** WER scores, VRAM usage, special features.

### Local Options

| # | Model | Params | VRAM | Framework | WER | Key Strengths |
|---|---|---|---|---|---|---|
| 1 ⭐ | `FunAudioLLM/SenseVoiceSmall` | 234M | ~250 MB | `funasr` | ~7-8% | Ultra-fast; emotion + audio events; 50+ languages |
| 2 | `openai/whisper-large-v3-turbo` | 809M | ~3-4 GB | `faster-whisper` | ~7.8% | 99+ languages; massive ecosystem |
| 3 | `openai/whisper-large-v3` | 1.55B | ~6-10 GB | `faster-whisper` | ~7.4% | Highest-accuracy Whisper variant |
| 4 | `nvidia/parakeet-tdt-1.1b` | 1.1B | ~2-4 GB | `nemo_toolkit` | ~8% | 2000-3000x real-time; English-focused |
| 5 | `ibm-granite/granite-speech-4.1-2b` | 2B | ~4-8 GB | `transformers` | ~5.3% | SOTA accuracy; LLM-integrated |

### Cloud Options

| # | Model | Provider | Key Strengths |
|---|---|---|---|
| 6 | `gemini/gemini-3.5-flash` | Google AI | Native audio understanding; transcribe + reason |
| 7 | `deepgram/nova-3` | Deepgram | Production-grade; real-time streaming; ~5% WER |

### ASR JSON Schema Contract
```json
{
  "text": "Full transcript...",
  "segments": [{"start": 0.0, "end": 2.5, "text": "Hello world", "confidence": 0.95}],
  "emotion": "happy|sad|neutral|angry",
  "audio_events": ["laughter", "applause"],
  "language": "en"
}
```
> Note: `emotion` and `audio_events` are only populated by SenseVoice-Small.

---

## 4. Embedding Models — Available Options

Selection criteria: **MTEB/MMTEB Leaderboard**, **CCKM Benchmark** (2026), modality support.

### Local Options

| # | Model | Params | VRAM | Dim | Modalities | Key Strengths |
|---|---|---|---|---|---|---|
| 1 ⭐ | `jinaai/jina-clip-v2` | ~900M | ~1 GB | 1024 | Text+Image | Open-weight; multilingual; Matryoshka |
| 2 | `nomic-ai/nomic-embed-vision-v2` | — | ~500 MB | 768 | Text+Image | Ultra-lightweight |
| 3 | `Qwen/Qwen3-Embedding-0.6B` | 600M | ~1.5 GB | 1024 | Text only | SOTA for size class on text benchmarks |

### Cloud Options

| # | Model | Dim | Modalities | Key Strengths |
|---|---|---|---|---|
| 4 ⭐ | `gemini/gemini-embedding-exp-03-07` | up to 3072 | Text+Image+Audio+Video+PDF | Best multimodal; free tier 10M tok/min |
| 5 | `voyage/voyage-multimodal-3` | 1024 | Text+Image | Strong retrieval accuracy |
| 6 | `openai/text-embedding-3-large` | 3072 | Text only | Industry standard; Matryoshka |

### Vector Dimension Rules
- Qdrant collection `syntraflow_chunks_v1` dimension determined by active embedding model.
- Default: **1024** (jina-clip-v2 native).
- Switching models with different dimensions requires a **re-indexing migration**.

---

## 5. Task Classifier / Router Models — Available Options

### Local Options

| # | Model | VRAM | Framework | Key Strengths |
|---|---|---|---|---|
| 1 ⭐ | `katanemo/Arch-Router-1.5B-Q8_0.gguf` | ~1-2 GB | `llama-cpp-python` | Purpose-built for routing; GGUF |
| 2 | `Qwen/Qwen3-4B-GGUF` (Q4_K_M) | ~2-3 GB | `llama-cpp-python` | Best accuracy/efficiency; LoRA fine-tunable |
| 3 | `microsoft/phi-4-mini-instruct-GGUF` | ~2-3 GB | `llama-cpp-python` | Edge-optimized; strong reasoning |
| 4 | `google/gemma-3-4b-it-GGUF` | ~2-3 GB | `llama-cpp-python` | Fast; multilingual |
| 5 | Semantic Router (Zero-VRAM) | 0 | Cosine similarity | Reuses embedding model; fastest |

### Cloud Options

| # | Model | Key Strengths |
|---|---|---|
| 6 | `gemini/gemini-3.5-flash` | JSON structured output; high accuracy |

### Classifier JSON Schema Contract
```json
{
  "complexity": "simple|medium|complex",
  "required_agents": ["retrieval", "coding", "web_search"],
  "confidence": 0.92
}
```

---

## 6. LLM Completion Providers — Available Options

### Providers & Models

| Provider | Model | Free Tier | Context | Strengths |
|---|---|---|---|---|
| Google AI ⭐ | `gemini/gemini-3.5-flash` | ~1500 RPD | 1M+ | Multimodal; fast; generous free tier |
| Google AI | `gemini/gemini-3.1-pro` | Lower | 2M+ | Deeper reasoning; largest context |
| Groq | `groq/llama-3.3-70b-versatile` | 30 RPM | — | Fastest inference (LPU) |
| Groq | `groq/qwen-qwq-32b` | 30 RPM | — | Strong reasoning at speed |
| OpenRouter | `openrouter/google/gemini-3.5-flash:free` | 20 RPM | — | Free aggregator |
| OpenRouter | `openrouter/qwen/qwen3-235b:free` | 20 RPM | — | Open-weight; strong coding/math |
| OpenRouter | `openrouter/meta-llama/llama-4-scout:free` | 20 RPM | — | General-purpose |
| Cerebras | `cerebras/llama-3.3-70b` | High | — | Long-context batch processing |
| Ollama (local) | `ollama/qwen3:8b` | ∞ | — | Fully offline; 4-6 GB VRAM |

### Default Fallback Chain
```python
COMPLETION_FALLBACKS = [
    "gemini/gemini-3.5-flash",                   # 1. Primary
    "groq/llama-3.3-70b-versatile",              # 2. Fast fallback
    "openrouter/google/gemini-3.5-flash:free",   # 3. Aggregator fallback
    "openrouter/qwen/qwen3-235b:free",           # 4. Open-weight fallback
    "openrouter/meta-llama/llama-4-scout:free",  # 5. Last resort
]
```

### Capability-Aware Truncation
| Tier | Truncation Limit |
|---|---|
| Frontier (Gemini 3.x) | No truncation (1M+) |
| Mid-tier (70B class) | 32,000 tokens |
| Small free-tier | 8,000 tokens |

### Provider API Keys
```env
GOOGLE_API_KEY=          # Google AI Studio (Gemini)
OPENROUTER_API_KEY=      # OpenRouter aggregator
GROQ_API_KEY=            # Groq
CEREBRAS_API_KEY=        # Cerebras
OPENAI_API_KEY=          # OpenAI (for DeepEval judge)
```

---

## 7. Model VRAM Budget Profiles

### Ultra-Low (8 GB VRAM — RTX 3060/4060)
| Role | Model | VRAM |
|---|---|---|
| OCR | Gemini 3.5 Flash (cloud) | 0 |
| ASR | SenseVoice-Small | 250 MB |
| Embedding | nomic-embed-vision-v2 | 500 MB |
| Classifier | Semantic Router | 0 |
| Completion | Gemini (cloud) | 0 |
| **Total** | | **~750 MB** |

### Standard (12-16 GB VRAM — RTX 4070/4080)
| Role | Model | VRAM |
|---|---|---|
| OCR | GLM-OCR (FP16) | 2 GB |
| ASR | SenseVoice-Small | 250 MB |
| Embedding | jina-clip-v2 | 1 GB |
| Classifier | Arch-Router-1.5B (GGUF) | 2 GB |
| Completion | Gemini (cloud) | 0 |
| **Total** | | **~5.25 GB** |

### High-End (24+ GB VRAM — RTX 3090/4090)
| Role | Model | VRAM |
|---|---|---|
| OCR | PaddleOCR-VL (FP16) | 3 GB |
| ASR | Granite Speech 4.1 2B | 6 GB |
| Embedding | jina-clip-v2 | 1 GB |
| Classifier | Qwen3-4B (GGUF Q4) | 3 GB |
| Completion | Ollama qwen3:8b (local) | 6 GB |
| **Total** | | **~19 GB** |

> Note: The VRAM Manager's lazy loading and LRU eviction means not all models need to be loaded simultaneously.
