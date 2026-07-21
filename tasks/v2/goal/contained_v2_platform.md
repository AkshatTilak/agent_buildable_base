# Goal: ContAIned AI Platform V2 — Dynamic, Real-Time & Modular

## Objective
Build **Version 2 (v2)** of the **ContAIned AI Platform**. While v1 laid down the static architecture and basic pipelines, v2 must transform the platform into a highly dynamic, real-time, and user-configurable orchestration environment. 

## System Value
The V2 upgrade fundamentally shifts the platform from a "hardcoded backend with a static UI" to a fully interactive AI framework where:
- **Granular Real-Time Frontend:** The frontend is deeply integrated via WebSockets/SSE to provide live telemetry (VRAM, processing status, active agents), breaking away from the static React monolith by leveraging Zustand for state management.
- **Visual Workflow Builder:** Users can visually construct and edit agent execution paths, fallbacks, and node/edge connections using a drag-and-drop ReactFlow interface.
- **Dynamic Ingestion Strategies (SyntraFlow):** Data ingestion isn't one-size-fits-all. Users can select chunking strategies (Semantic, Recursive) and pluggable pre-processing (OCR noise reduction) or post-processing (entity extraction).
- **Agent Management (CRUD):** A dedicated Agent Hub allows operators to dynamically view, create, and edit agents. You can tweak their system prompts, model assignments, and available tools without touching the backend code.
- **Automated Evaluation Generation:** Test cases for RAGAS/DeepEval are no longer detached scripts. Evaluations are dynamically tied to specific agents, allowing you to generate and attribute tests directly to the agent workflows you just built.
- **Clean Architecture:** APIs are decoupled, circular dependencies are resolved, and strict boundaries are enforced between gateway, inference, and common services.

## Completion State
This Goal is achieved when **all** Base Tasks below are verified and marked `[x]`:

- [x] `base/01_frontend_granular_realtime.md` — Decoupled React+Vite frontend with Zustand and WebSockets/SSE.
- [ ] `base/02_visual_workflow_builder.md` — ReactFlow drag-and-drop LangGraph visual editor.
- [ ] `base/03_ingestion_pipeline_strats.md` — Selectable chunking and pre/post-processing pipelines.
- [x] `base/04_agent_management_crud.md` — APIs and UI for Agent CRUD.
- [ ] `base/05_eval_test_generation.md` — Agent-attributed eval generation and integration.
- [ ] `base/06_architecture_cleanup.md` — Decoupling and refactoring existing backend logic.
