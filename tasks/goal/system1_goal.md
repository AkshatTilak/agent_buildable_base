# Goal: EchoMind Core — General Agent System (System 1)

## The North Star

Build a **complete, production-ready Enterprise AI Agent Orchestration System** that provides:

1. **Multi-Role RBAC** — User management with role-based access control (standard, premium, admin) enforced at both API and tool-execution levels.
2. **LangChain Agent Orchestration** — Intelligent routing of user queries to appropriate tools with conversational context management.
3. **RAGFlow Document Understanding** — Superior document parsing with OCR, layout recognition, and semantic chunking via the RAGFlow engine.
4. **pgvector Similarity Search** — HNSW-indexed cosine similarity search over document embeddings stored in PostgreSQL.
5. **Kafka Async Streaming** — Decoupled document ingestion pipeline using Kafka producer/consumer pattern.
6. **Redis Caching Layer** — Sub-millisecond session caching, chat history (RedisChatMessageHistory), and token-bucket rate limiting.
7. **Langfuse Observability** — Full LLM telemetry, prompt tracing, and evaluation via self-hosted Langfuse.
8. **Langflow Visual Builder** — Drag-and-drop agent creation with native Langfuse integration.
9. **PySpark Analytics** — Batch ETL pipeline for user analytics with partitioned JDBC reads/writes.

## Completion Criteria

This Goal is met when **all 9 Base Tasks** below are verified and marked `[x]`:

- [x] `base/BT01_infrastructure_setup.md` — Docker & Infrastructure ✅ (2026-07-13)
- [x] `base/BT02_database_architecture.md` — Database & Vectors ✅ (2026-07-14)
- [x] `base/BT03_auth_and_rbac.md` — Authentication & RBAC ✅ (2026-07-14)
- [ ] `base/BT04_agent_orchestration.md` — LangChain Agent
- [ ] `base/BT05_rag_integration.md` — RAGFlow Integration
- [ ] `base/BT06_kafka_streaming.md` — Kafka Pipeline
- [ ] `base/BT07_observability.md` — Langfuse Telemetry
- [ ] `base/BT08_analytics_pipeline.md` — PySpark ETL
- [ ] `base/BT09_langflow_integration.md` — Langflow Visual Builder

## Project Location

- **Source Code**: `../../echomind-core/`
- **Research Document**: `../../Agent System Architecture Research.md`
