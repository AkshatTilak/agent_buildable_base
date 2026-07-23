# V5 Task Registry & Roll-Up SOP

> **Version:** 5 — Platform Maturity  
> **Status:** `[ ] Active`

---

## Base Tasks → Subtask Roll-Up

| Base Task ID | Title | Subtask Count | Status |
|---|---|---|---|
| **B5-01** | EvalOps Deep Integration (RAGAS + DeepEval + Datasets + Dashboard) | 8 | `[ ]` |
| **B5-02** | Agent Endpoints & Routing API | 5 | `[ ]` |
| **B5-03** | User Authentication & RBAC | 6 | `[x]` |
| **B5-04** | Model Playground | 5 | `[ ]` |
| **B5-05** | MCP Integration Hub | 5 | `[ ]` |
| **B5-06** | Workflow Builder Enhancements | 7 | `[ ]` |
| **B5-07** | External API Gateway | 5 | `[ ]` |

**Total Subtasks:** 41

---

## Subtask Index

### B5-01: EvalOps Deep Integration
- `[ ]` S5-01a — RAGAS Evaluation Runner
- `[ ]` S5-01b — DeepEval Evaluation Runner
- `[ ]` S5-01c — Custom Dataset Manager (CRUD + Import/Export)
- `[ ]` S5-01d — Enhanced Eval Run Orchestration (framework/metric selection)
- `[x]` S5-01e — EvalOps DB Schema Expansion (EvalMetricResult, EvalRunHistory columns)
- `[ ]` S5-01f — EvalOps Dashboard API (stats, trends)
- `[ ]` S5-01g — Frontend: Suite Manager Tab
- `[ ]` S5-01h — Frontend: Results Dashboard (metrics cards, trend charts, drill-down)

### B5-02: Agent Endpoints & Routing API
- `[x]` S5-02a — Agent Invocation Endpoint (`POST /api/agents/{id}/invoke`)
- `[x]` S5-02b — Unified Routing Endpoint (`POST /api/route`)
- `[x]` S5-02c — Agent Invocation Logging (DB table + stats query)
- `[ ]` S5-02d — Agent CRUD Enhancements (is_active, endpoint_slug, stats)
- `[ ]` S5-02e — Frontend: Agent Invoke Panel & Stats

### B5-03: User Authentication & RBAC
- `[x]` S5-03a — Auth Package Scaffold & DB Models (User, UserSession)
- `[x]` S5-03b — Google OAuth Provider
- `[x]` S5-03c — GitHub OAuth Provider
- `[x]` S5-03d — JWT Middleware & Role-Based Dependencies
- `[x]` S5-03e — Frontend: Login Page & Auth State Management
- `[x]` S5-03f — Frontend: Route Guards, Header User Badge, Admin User Management

### B5-04: Model Playground
- `[ ]` S5-04a — Playground Chat API (completion, streaming, model selection)
- `[ ]` S5-04b — File Upload & Processing Pipeline (PDF, image, video)
- `[ ]` S5-04c — Playground Session Persistence (save/load)
- `[ ]` S5-04d — Frontend: PlaygroundPage Chat UI
- `[ ]` S5-04e — Frontend: File Attachment Zone & Session Sidebar

### B5-05: MCP Integration Hub
- `[ ]` S5-05a — MCP Server Registry (CRUD + health checks)
- `[ ]` S5-05b — Tool Discovery & Caching
- `[ ]` S5-05c — Tool Invocation API & Testing
- `[ ]` S5-05d — Internal MCP Auto-Registration (SyntraFlow)
- `[ ]` S5-05e — Frontend: MCPHubPage (server list, tool panels, test invoker)

### B5-06: Workflow Builder Enhancements
- `[ ]` S5-06a — IfElseNode (frontend component + conditional evaluator runtime)
- `[ ]` S5-06b — WebhookNode (frontend component + HTTP executor runtime)
- `[ ]` S5-06c — APICallNode (frontend component + REST executor runtime)
- `[ ]` S5-06d — EvalNode & MCPToolNode (frontend + runtime)
- `[ ]` S5-06e — RouterNode & TransformNode (frontend + runtime)
- `[ ]` S5-06f — Graph Parser V5 (parse all new node types into LangGraph)
- `[ ]` S5-06g — Dynamic Node Palette (auto-populate from agents, MCP tools, eval suites)

### B5-07: External API Gateway
- `[ ]` S5-07a — OpenAI-Compatible `/v1/chat/completions` Endpoint
- `[ ]` S5-07b — Embeddings & Models List Endpoints (`/v1/embeddings`, `/v1/models`)
- `[ ]` S5-07c — API Key Management CRUD & Middleware
- `[ ]` S5-07d — Per-Key Rate Limiting & Usage Tracking
- `[ ]` S5-07e — Frontend: API Keys Tab in Settings + Docs Panel

---

## Execution Order (Recommended)

Dependencies flow top-down. Bolded items should be prioritized.

1. **B5-03** (Auth) — foundational; all other features reference `user_id`
2. **B5-01e** (DB schema) — schema must exist before eval runners write to it
3. **B5-02** (Agent Endpoints) — invocation infra used by Playground and External API
4. **B5-01** (EvalOps) — runners, datasets, dashboard
5. **B5-05** (MCP Hub) — tool registry used by Workflow Builder
6. **B5-04** (Playground) — depends on agent invoke + file processing
7. **B5-06** (Workflow Builder) — depends on agents, MCP tools, evals all existing
8. **B5-07** (External API) — depends on agent invoke + API key management

---

## Roll-Up Rules
- A Base Task is marked `[x]` only when **all** its linked Subtasks are `[x]`.
- The V5 Goal is met when **all 7 Base Tasks** are `[x]`.
