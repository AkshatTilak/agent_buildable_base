# V5 Goal: Platform Maturity — Evals, Auth, Playground, MCP, Routing & Workflow Intelligence

> **Version:** 5  
> **Status:** `[ ] Active`  
> **Depends On:** V1–V4 (Completed)

---

## North Star Objective

Transform ContAIned from a functional "buildable base" into a **production-grade AI operations platform** by delivering seven interconnected feature areas that span every submodule:

1. **EvalOps Deep Integration** — Full RAGAS + DeepEval evaluation pipelines with custom dataset management and comprehensive metrics dashboards (recall, faithfulness, precision, relevance, hallucination, toxicity, bias).
2. **Agent Endpoints & Routing API** — Per-agent invocation endpoints, unified prompt routing, invocation logging and stats.
3. **User Authentication & RBAC** — Google and GitHub OAuth login, JWT sessions, admin/editor/viewer role enforcement across all dashboard routes.
4. **Model Playground** — Interactive chat with any registered model, file attachment support (PDF, images, video), session persistence.
5. **MCP Integration Hub** — Management UI for internal and external MCP servers, tool discovery, inline testing, and seamless workflow builder integration.
6. **Workflow Builder Enhancements** — IfElse logic blocks, Webhook/API Call nodes, Router nodes, Eval nodes, MCP Tool nodes, Transform nodes, and a dynamic auto-populating node palette.
7. **External API Gateway** — OpenAI-compatible `/v1/chat/completions` endpoint, API key management, per-key rate limiting, usage tracking.

---

## Completion Criteria

This goal is met when **all seven Base Tasks** (`tasks/v5/base/B5-01` through `B5-07`) and their linked Subtasks are marked `[x]`, the platform builds cleanly, and all verification tests pass.
