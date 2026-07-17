# Goal: ContAIned AI Platform — Production-Ready System

## Objective
Build a fully operational, production-ready **ContAIned AI Platform** — a modular, AI-first monorepo that autonomously handles document/video ingestion (SyntraFlow), intelligent query orchestration (GuardRoute), automated QA (EvalOps), and a unified developer dashboard (Frontend), all backed by a configurable model registry and robust infrastructure.

## System Value
The ContAIned platform provides a self-contained AI application framework where:
- **SyntraFlow** ingests, processes, and indexes documents/videos into a hybrid RAG store (Postgres + Qdrant + Neo4j).
- **GuardRoute** classifies user queries, orchestrates parallel subagents via LangGraph, and synthesizes responses with multi-provider LLM fallback.
- **EvalOps** runs automated safety, accuracy, and performance benchmarks as a CI/QA tool.
- **The Frontend** provides a unified React dashboard for monitoring, interacting, and administering all subsystems.
- **The Model Registry** enables operators to swap between local GPU and cloud API models per task role without code changes.

## Completion State
This Goal is achieved when **all** Base Tasks below are verified and marked `[x]`:

- [x] `base/common_library.md` — Shared library config, database clients, schemas, observability
- [x] `base/syntraflow_rag.md` — Document and video ingestion, hybrid RAG store, MCP tools
- [x] `base/guardroute_orchestrator.md` — Query classification, LangGraph orchestrator, fallbacks, streaming
- [x] `base/model_hub.md` — HuggingFace integration, quantization, device mapping
- [x] `base/inference_models.md` — Inference server model endpoints (classifier, ASR, embedding, OCR)
- [x] `base/evalops_qa.md` — Full EvalOps QA pipeline
- [ ] `base/frontend_ui.md` — Complete React developer dashboard
- [x] `base/security_hardening.md` — Security hardening across all layers
- [x] `base/system_lifecycle.md` — Startup/shutdown lifecycle & graceful degradation
