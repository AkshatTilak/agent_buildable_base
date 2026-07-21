# Frontend UI & Dashboard Architecture

> **Source:** Migrated from `requirements/frontend.md`
> **Last Updated:** 2026-07-14

This document details the user interface, component layout, dynamic route loading parameters, and technical stack of the unified web client for the ContAIned AI Platform.

---

## 1. Technical Stack (Pending)

### Framework & Build Tool
- **Framework:** React 18+ with TypeScript.
- **Build Tool:** Vite.
- **Location:** `frontend/` at monorepo root.
- **State Management:** `zustand` (stores in `frontend/src/store/`: `metricsSlice.ts`, `agentSlice.ts`, `workflowSlice.ts`).
- **Real-Time Telemetry:** `TelemetryService` (`frontend/src/services/telemetry.ts`) with WebSocket (`/api/telemetry/ws`) & SSE fallback.
- **Key Libraries:**
  - `@xyflow/react` (React Flow) — LangGraph execution flowcharts.
  - `zustand` — modular state management.
  - `recharts` — EvalOps metric charts.
  - `@tanstack/react-query` — API data fetching and caching.
  - `react-markdown` + `react-syntax-highlighter` — chat rendering.
  - `lucide-react` — icon library.

### API Client Layer
- REST (`fetch`/`axios`) for gateway communication.
- SSE for streaming chat responses from GuardRoute.
- Auto-generate TypeScript types from FastAPI's `/openapi.json`.
- Base URL: `VITE_API_URL` (default: `http://localhost:8000`).

### Docker Integration
- Add `frontend` service to `docker-compose.yml`.
- Alternative: Serve built static files from gateway via `fastapi.staticfiles`.

---

## 2. Design Tokens & Aesthetics (Pending)
- **Dark Mode:** Graphite backgrounds (`#0F0F11` to `#18181B`) with neon accents (emerald/indigo/amber).
- **Typography:** Google Fonts — `Inter` or `Outfit`.
- **Glassmorphism:** `backdrop-filter: blur(12px)`, thin borders (`1px solid rgba(255,255,255,0.05)`).
- **Micro-Animations:** Cubic-bezier transitions (`0.4, 0, 0.2, 1`).
- **Responsive:** Optimized for desktop/tablet (min 1024px). No mobile requirement.

---

## 3. Pluggable Dashboard Architecture (Pending)
- **Status Mapping:** GET `/health` → read `active_projects` array.
- **Dynamic Navigation:** Disable routes/menus for inactive modules.
- **Fallback Placeholders:** Show status card with enable instructions if user navigates to disabled module.

---

## 4. Global System Status Panel (Pending)

### Connection Grid
| Service | Port |
|---|---|
| Gateway | 8000 |
| Inference Server | 8010 |
| PostgreSQL | 5432 |
| Qdrant | 6333 |
| Neo4j | 7687 |
| Redis | 6379 |
| Kafka Broker | 9092 |

### VRAM Monitor
- Visual gauge: VRAM used / budget.
- Live list of loaded model slots (from `/health`).
- Cold-start vs warm latency metrics.

### Model Registry Panel
- Active model per role (OCR, ASR, Embedding, Classifier, Completion).
- Local vs cloud indicator per role.
- VRAM budget impact visualization.
- Model benchmark scores/docs links.
- Toggle model selection via API (if `AUTH_ENABLED`).

### Admin Quick-Console Links
- Qdrant: `http://localhost:6333/dashboard`
- Neo4j: `http://localhost:7474`
- pgAdmin: `http://localhost:5050`
- Kafka UI: `http://localhost:8080`
- Jaeger: `http://localhost:16686`

---

## 5. SyntraFlow Dashboard (Pending)
- **Uploader:** Multi-modal drag-and-drop. Upload speeds, file categories, duplicate detection.
- **Ingestion Job Tracker:** Active/recent jobs with status, progress bar, job logs.
- **Layout OCR Split Panel:** Left: PDF renderer. Right: Extracted Markdown. Shows OCR model used.
- **Video Segment Timeline:** HTML5 player + transcription segments + keyframe descriptions + event tags.
- **RAG Sandbox:** Query box, strategy selector (Vector/Graph/Hybrid), side-by-side results comparison.

---

## 6. GuardRoute Dashboard (Pending)
- **LangGraph Flowchart:** React Flow interactive flow map with real-time node highlighting and latency annotations.
- **Subagent Node Logs:** Side-drawer with raw JSON, latency, model, token count.
- **Streaming Chat Client:** Markdown + syntax highlighting. SSE streaming. Meta-header with complexity, agents, latency, model, tokens.

---

## 7. EvalOps Dashboard (Pending)
- **RAGAS Metric Charts:** Timeline graphs for Context Recall, Faithfulness, Semantic Similarity.
- **Safety Audit Logs:** Blocked injections, toxicity ratings, PII leakage alerts.
- **Diagnostic Trace Waterfall:** OpenTelemetry span visualization with bottleneck highlighting.
- **Model Comparison Dashboard:** Benchmark results from `bench_models.py`.

---

## 8. Error Handling & Loading States (Pending)
- **Skeleton Loaders:** Animated placeholders during data load.
- **Error Banners:** Dismissible with message, retry button, timestamp, suggestion.
- **Upload Failures:** Specific errors for size limit, unsupported format, duplicate, pipeline failure.
- **LLM Provider Outage:** Show provider status and fallback model used.
- **Empty States:** Meaningful illustrations for empty dashboards.

---

## 9. Admin Services (docker-compose)

### Admin Profile (`--profile admin`)
```yaml
pgadmin:
  image: dpage/pgadmin4
  ports: ["5050:80"]
  environment:
    PGADMIN_DEFAULT_EMAIL: admin@local.dev
    PGADMIN_DEFAULT_PASSWORD: admin
  profiles: ["admin"]

kafka-ui:
  image: provectuslabs/kafka-ui:latest
  ports: ["8080:8080"]
  environment:
    KAFKA_CLUSTERS_0_NAME: local
    KAFKA_CLUSTERS_0_BOOTSTRAPSERVERS: kafka:9092
  profiles: ["admin"]
```

### Observability Profile (`--profile observability`)
```yaml
jaeger:
  image: jaegertracing/all-in-one:latest
  ports:
    - "16686:16686"
    - "4317:4317"
    - "4318:4318"
  profiles: ["observability"]
```
