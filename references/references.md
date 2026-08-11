# Reference Management & Contextual Memory (`references.md`)

This directory serves as the centralized knowledge base and memory bank for all AI agents and human developers working on the ContAIned AI Platform. It stores architectural decisions, codebase context, deployment guidelines, microservice specifications, and operational standards.

**Agent Instruction:** Before executing a task, query the relevant subdirectories here to understand existing paradigms. Upon completing a complex task, updating these reference files is mandatory to ensure downstream agents retain context. All command executions across deployment and infrastructure tasks must strictly adhere to **Poetry** (`poetry run ...`, `poetry add ...`, `poetry install --all-extras`).

---

## 1. Directory Structure & Usage Protocol

All contextual knowledge must be strictly categorized into the following subdirectories. Do not dump raw data into the root folder.

### `logic/` (Business Rules & System Flow)
* **Purpose:** Stores core business logic, decision matrices, and AI/system interaction flows.
* **Contents:** State machine definitions, prompt engineering strategies, core algorithmic rules, and data validation requirements.
* **Key Files:**
  * [hubs.md](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/hubs.md): Canonical multi-tenant hub platform architecture, scoping rules, DB schema, and API routes.
  * [user_management.md](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/user_management.md): V7 admin user lifecycle, soft deletion, invitations, approval gate, and audit logging.
  * [auth.md](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/auth.md): V7 auth model including soft-deletion, session revocation on logout, and environment-driven super-admin bootstrapping.
  * [workflow_v6.md](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/workflow_v6.md): Multi-workflow builder architecture and version control.
  * [workflow_v5.md](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/workflow_v5.md): Workflow Builder V5 node types and graph parser.
  * [syntraflow.md](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/syntraflow.md): Ingestion engine, dynamic collection bindings, and V7 Modular Multi-Step Configurable Ingestion Pipeline (OCR, Chunking, Embedding, Summarization, KG Extraction).
  * [guardroute.md](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/guardroute.md): Agent execution and routing architecture.
  * [evalops.md](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/evalops.md): Evaluation engine and polymorphic test targets.
  * [agent_endpoints.md](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/agent_endpoints.md): Agent execution endpoints and routing logic.
  * [external_api.md](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/external_api.md): External API Gateway architecture and routes.
  * [inference.md](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/inference.md): GPU model inference server architecture and VRAM management.
  * [mcp_hub.md](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/mcp_hub.md): Model Context Protocol (MCP) Integration Hub.
  * [model_registry.md](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/model_registry.md): Model options, VRAM profiles, dynamic LiteLLM discovery, disk path resolution & cache status (`HF_HOME` & `./models/`), Harrier 0.6B embedder, and model deletion with disk purging (`DELETE /api/models/local/{model_id}?purge_disk=true`).
  * [playground.md](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/playground.md): Interactive Model Playground architecture.
  * [security.md](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/security.md): System security policies, token management, and encryption standards.
* **When to use:** Read this to understand *why* a feature behaves the way it does. Update this when altering fundamental system behavior.

### `structure/` (Architecture & Topology)
* **Purpose:** Defines the physical and logical layout of the system.
* **Contents:** Database schemas, API routing maps, dependency graphs, frontend visual tokens, and monorepo folder architecture.
* **Key Files:**
  * [repository_layout.md](file:///c:/Akshat/ContAIned/agent_buildable_base/references/structure/repository_layout.md): High-level monorepo folder layout, module responsibilities, integration flows, and directory navigation.
  * [system_architecture.md](file:///c:/Akshat/ContAIned/agent_buildable_base/references/structure/system_architecture.md): Monorepo rules, environment settings, database/message queue specifications, and system lifecycle configurations.
  * [frontend.md](file:///c:/Akshat/ContAIned/agent_buildable_base/references/structure/frontend.md): Hub Shell UI, navigation patterns, workspace layouts, and information architecture.
  * [design_system.md](file:///c:/Akshat/ContAIned/agent_buildable_base/references/structure/design_system.md): System design tokens, color palettes, typography, and component styling rules.
* **When to use:** Read this when creating new modules to ensure they fit the existing topology. Update this when adding new tables, microservices, or core integrations.

### `code/` (Codebase Explanations & Mappings)
* **Purpose:** Explains complex implementations that are not immediately obvious from reading raw source code.
* **Contents:** Shared library structures, file-to-feature mappings, breakdowns of complex algorithms, custom class wrappers, and historical context.
* **Key Files:**
  * [common_library.md](file:///c:/Akshat/ContAIned/agent_buildable_base/references/code/common_library.md): Shared `common/` package documentation, DB clients, and logging setup.
* **When to use:** Query this when interacting with legacy code or highly abstracted AI wrappers.

### `deployment/` (Infrastructure & Execution)
* **Purpose:** Contains commands and configurations required to run, test, and deploy the system.
* **Contents:** Docker compose profiles, CI/CD pipeline structures, environment templates (`.env.example`), server setup scripts, and Poetry execution standards.
* **Key Files:**
  * [infrastructure.md](file:///c:/Akshat/ContAIned/agent_buildable_base/references/deployment/infrastructure.md): Docker setup, profile configurations (e.g., `--profile core`), and container orchestration.
  * [deployment_guide.md](file:///c:/Akshat/ContAIned/agent_buildable_base/references/deployment/deployment_guide.md): Environment deployment guide and operational setup steps.
  * Local persistence is now anchored to host bind mounts under [data/](file:///c:/Akshat/ContAIned/data) for Postgres, Qdrant, Redis, and Neo4j to keep Docker and local development state consistent.
* **When to use:** Use this to spin up test environments or production builds. Always execute Python tasks using `poetry run` commands.

### `issues/` (Technical Debt & Blockers)
* **Purpose:** Tracks known bugs, architectural bottlenecks, and unresolved system flaws.
* **Contents:** Post-mortems of failed integrations, notes on deprecated libraries, and edge-cases.
* **When to use:** Check this before starting a task to ensure you aren't building on top of a broken dependency.

### `resource/` (External Knowledge & Assets)
* **Purpose:** Catalogs external tools, documentation, and assets relied upon by the project.
* **Contents:** Links to specific API doc versions (e.g., Poetry, LangChain, FastAPI, DeepEval), required datasets, or third-party service limits.
* **When to use:** Reference this to verify library capabilities before implementation.

### `user/` (Human-in-the-Loop Actions)
* **Purpose:** Defines tasks, approvals, and configurations that *only* the human developer can execute.
* **Contents:** Requests for API keys, billing setup instructions, manual QA testing requirements, or UI/UX visual approvals.
* **When to use:** Log requirements here and halt execution if an external human boundary (e.g., API key) is reached.

---

## 2. Read / Write Guidelines for Agents

* **Search Before You Build:** Always perform a workspace search within this `references/` directory before writing new core logic to prevent duplicating existing structures.
* **Keep It Modular:** Create isolated `.md` files within subdirectories for specific topics.
* **Timestamp & Link:** When noting an issue or code explanation, link back to the exact task or requirement file that generated it.