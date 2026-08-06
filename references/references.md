explain usage and structure here.
# Reference Management & Contextual Memory (`references.md`)

This directory serves as the centralized knowledge base and memory bank for all AI agents and human developers working on the project. It stores architectural decisions, codebase context, unresolved issues, deployment guidelines, and human-in-the-loop tasks.

**Agent Instruction:** Before executing a task, query the relevant subdirectories here to understand existing paradigms. Upon completing a complex task, updating these reference files is mandatory to ensure downstream agents retain context.

---

## 1. Directory Structure & Usage Protocol

All contextual knowledge must be strictly categorized into the following subdirectories. Do not dump raw data into the root folder. 

### `logic/` (Business Rules & System Flow)
* **Purpose:** Stores the core business logic, decision matrices, and AI/system interaction flows.
* **Contents:** State machine definitions, prompt engineering strategies, core algorithmic rules, data validation requirements, and [PDF Spatial Ingestion & Extraction Rules](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/references/logic/ingestion_extraction_rules.md).
* **When to use:** Read this to understand *why* a feature behaves the way it does. Update this when altering fundamental system behavior.

### `structure/` (Architecture & Topology)
* **Purpose:** Defines the physical and logical layout of the system.
* **Contents:** Database schemas, API routing maps, dependency graphs, and monorepo/folder architecture diagrams.
* **When to use:** Read this when creating new modules to ensure they fit the existing topology. Update this when adding new tables, microservices, or core integrations.

### `code/` (Codebase Explanations & Mappings)
* **Purpose:** Explains complex implementations that are not immediately obvious from reading the raw source code.
* **Contents:** File-to-feature mappings, breakdowns of complex algorithms, custom class wrappers, and historical context on refactored changes.
* **When to use:** Query this when interacting with legacy code or highly abstracted AI wrappers. 

### `deployment/` (Infrastructure & Execution)
* **Purpose:** Contains all commands and configurations required to run, test, and deploy the system.
* **Contents:** Docker commands, CI/CD pipeline structures, environment variable templates (`.env.example`), and server setup scripts.
* **When to use:** Use this to provide the user with exact copy-paste commands to spin up test environments or production builds.

### `issues/` (Technical Debt & Blockers)
* **Purpose:** Tracks known bugs, architectural bottlenecks, and unresolved system flaws.
* **Contents:** Post-mortems of failed integrations, notes on deprecated libraries, and edge-cases that models were unable to rectify.
* **When to use:** Check this before starting a task to ensure you aren't building on top of a broken dependency. Add to this if a task is blocked by external factors.

### `resource/` (External Knowledge & Assets)
* **Purpose:** Catalogs external tools, documentation, and assets relied upon by the project.
* **Contents:** Links to specific versions of API docs (e.g., LangChain v0.3, FastAPI), required datasets, or third-party service limits.
* **When to use:** Reference this to verify library capabilities before hallucinating non-existent functions.

### `user/` (Human-in-the-Loop Actions)
* **Purpose:** Defines tasks, approvals, and configurations that *only* the human developer can execute.
* **Contents:** Requests for API keys, billing setup instructions, manual QA testing requirements, or UI/UX visual approvals.
* **When to use:** If an agent reaches a hard boundary (e.g., needing an OpenAI key or a Vercel deployment token), log the requirement here and halt that execution path.

---

## 2. Read / Write Guidelines for Agents

* **Search Before You Build:** Always perform a workspace search within this `references/` directory before writing new core logic to prevent duplicating existing structures.
* **Keep It Modular:** Create isolated `.md` files within the subdirectories for specific topics (e.g., `structure/database_schema.md`, `deployment/docker_setup.md`).
* **Timestamp & Link:** When noting an issue or code explanation, link back to the exact task or requirement file that generated it, and provide a rough timestamp or version number for traceability.