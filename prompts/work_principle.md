# Standard Operating Procedure: Task Execution & Principles (`work_principle.md`)

This document outlines the strict protocol, execution standards, and operational guidelines for all agents and developers when selecting, executing, integrating, and documenting tasks within this workspace.

---

## 1. Task Selection Limits & Prioritization

- **Max Active Load:** You may select and work on **no more than 3 subtasks** simultaneously to maintain deep context and execution quality.
- **Dependency-First Prioritization:** Work must be sequenced to unblock the most critical system components first:
  1. **AI & Core Services:** Prioritize AI model integrations, prompt engineering structures, inference pipelines, and core agent architectures first.
  2. **Backend & Infrastructure:** Following AI services, prioritize foundational backend tasks (e.g., database schemas, shared utilities, Docker setups, and message brokers).
  3. **Upstream Submodules & UI:** Only after the AI and backend foundations are stable should work begin on downstream dependencies or UI wrappers.

---

## 2. Core Execution Principles & Poetry Standard

### Context Before Code (Adhere to [`references.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/references.md))
- **Query Memory First:** Before implementing complex logic, you **must** check the `references/` directory (e.g., [`references/logic/`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic), [`references/structure/`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/structure), [`references/code/`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/code), or [`references/deployment/`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/deployment)) to ensure your implementation aligns with the Architect's systemic vision.
- **Update the Bank:** If you make a significant design choice or architectural decision, immediately document it in the appropriate `references/` subfolder. If technical debt or a blocker is encountered, document it in [`references/issues/`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/issues).

### Complete & Integrated Delivery
- Every task must be completed **fully and end-to-end**, integrating across all dependent submodules.
- **Never implement duplicate code.** Abstract recurring logic into a shared utility or `common/` package.
- **Run infrastructure & inference to test:** Execute and verify against the **actual running development environment and services on standard ports** (Postgres `:5432`, Qdrant `:6333`, Redis `:6379`, Neo4j `:7687`, Kafka `:9092`, Gateway `:8000`, Inference `:8010`). Do NOT spin up separate test container stacks or divergent ports.
- **Test data retention:** Deleting data created by tests is **not necessary**; utilize unique namespaces/UUIDs for test entity isolation.
- **Inspect Docker container logs & fix root causes:** When tests encounter errors or expose flaws, you must inspect **Docker container logs** (`docker compose logs <service>`) and **actively fix the underlying backend, frontend, inference, or submodule code** (SyntraFlow, GuardRoute, EvalOps, Common Library) rather than skipping, ignoring, or mocking around the failures.

### Mandatory Poetry Command Standard
All Python command execution, dependency management, testing, and script runs MUST strictly use **Poetry**. Direct execution of system Python or un-managed virtualenv binaries is prohibited.

- **Dependency Management:**
  - Synchronize environment: `poetry install --all-extras`
  - Add core package: `poetry add <package>`
  - Add dev dependency: `poetry add <package> --group dev`
  - Add submodule extra: `poetry add <package> --extras <extra_name>` (e.g., `syntraflow`, `guardroute`, `inference`, `evalops`)
  - Check lockfile status: `poetry check`
- **Execution & Dev Servers:**
  - Run arbitrary Python scripts: `poetry run python <script_path>`
  - Run FastAPI server / Uvicorn: `poetry run uvicorn gateway.main:app --reload`
- **Testing & Quality Assurance:**
  - Run test suite: `poetry run pytest`
  - Run DeepEval AI benchmarks: `poetry run deepeval run test <test_file>`
  - Code formatting & linting: `poetry run ruff check .`, `poetry run black --check .`, `poetry run mypy .`
- **Database Migrations:**
  - Apply migrations: `poetry run alembic upgrade head`
  - Create new migration: `poetry run alembic revision --autogenerate -m "<description>"`

---

## 3. Scope Management & Tracking (Adhere to [`tasks.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/tasks.md))

### Out-of-Scope Issues & The `temp/` Directory
If you encounter a bug, missing dependency, or design flaw that falls **beyond the scope of your current Subtask**:
  1. Do **NOT** attempt to resolve it silently.
  2. Following [`tasks.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/tasks.md), create a temporary markdown log in the active version folder `tasks/v<N>/temp/` to track the issue.
  3. Immediately return focus solely to your assigned Subtask.

### Status Roll-Ups
Always update the status of checklist items (`[ ]` to `[/]` to `[x]`). Remember the Roll-Up Rule: A Base Task is only complete when all of its linked Subtasks are verified and marked `[x]`.

---

## 4. Quality, Design, & Maintainability

### Standard Architectural Practices
- **Design Patterns:** Adhere to strictly typed, modern design patterns appropriate for Python (e.g., explicit typing, Pydantic v2 schemas, and dataclasses).
- **Observability First:** Ensure proper logging (`common.observability.logger`), telemetry, and OpenTelemetry transaction tracing are integrated natively into all new features.
- **Testing & Verification:** Write tests concurrently with feature development. Adhere to the verification protocol in [`work_verification.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/prompts/work_verification.md). Prioritize evaluation benchmarks and safety checks for AI/inference pipelines (`poetry run deepeval`), followed by unit and integration tests (`poetry run pytest`).
- **Containerization:** Utilize Docker (or the project's equivalent containerization strategy) to spin up isolated environments for databases, vector stores, testing, or service dependencies.
- **Dependency Management:** Always manage dependencies through Poetry in [`pyproject.toml`](file:///c:/Akshat/ContAIned/pyproject.toml). Ensure packages are assigned to the correct core list, optional extras, or dev groups.

### Clarity & Collaboration
- **Stop and Ask:** If a requirement, contract, or design decision is ambiguous, presents an architectural flaw, or lacks logical sense, **stop and ask for clarification** before making assumptions.
- **System Architect Coordination:** Align with system-level architectural directions as set out by [`agent.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/agent.md).
- **Documentation Updates:** If you introduce new configurations, architectural improvements, or setup flags, immediately update the project-level [`README.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/README.md) so other agents and developers are aware.
- **Environment Variables:** If valid environment variables, API keys, or custom `.env` parameters are required to test or execute any module, explicitly prompt for these values and update `.env.example`.

### Documentation & Version Verification
- **Verify Before Coding:** Before modifying or adding code, consult the official online documentation of the respective library to ensure implementation aligns with modern best practices.
- **Strict Versioning:** Always cross-reference your implementation with the exact library versions defined in [`pyproject.toml`](file:///c:/Akshat/ContAIned/pyproject.toml) via Poetry to prevent deprecation errors or version conflicts.
- **Official Documentation Sources:** Refer to these resources for packages listed in [`pyproject.toml`](file:///c:/Akshat/ContAIned/pyproject.toml):
  - **Dependency & Package Management:** [Poetry Docs](https://python-poetry.org/docs/)
  - **Hugging Face & ML:** [Hugging Face Docs](https://huggingface.co/docs), [Transformers Docs](https://huggingface.co/docs/transformers/index), [Sentence Transformers](https://sbert.net/)
  - **LLM Orchestration & Clients:** [LangChain Docs](https://python.langchain.com/v0.3/docs/introduction/), [LangGraph Docs](https://langchain-ai.github.io/langgraph/), [LiteLLM Docs](https://docs.litellm.ai/)
  - **API & Data Validation:** [FastAPI Docs](https://fastapi.tiangolo.com/), [Pydantic v2 Docs](https://docs.pydantic.dev/latest/)
  - **Database & Storage:** [SQLAlchemy v2 Docs](https://docs.sqlalchemy.org/en/20/), [Asyncpg Docs](https://magicstack.github.io/asyncpg/current/), [Qdrant Docs](https://qdrant.tech/documentation/), [Neo4j Python Driver](https://neo4j.com/docs/python-manual/current/)
  - **Evaluation & MLOps:** [DeepEval Docs](https://docs.confident-ai.com/), [Ragas Docs](https://docs.ragas.io/en/stable/), [MLflow Docs](https://mlflow.org/docs/latest/index.html)
  - **Other Integrations:** [Model Context Protocol (MCP)](https://modelcontextprotocol.io/), [Llama.cpp Python](https://abetlen.github.io/llama-cpp-python/), [Confluent Kafka Python](https://docs.confluent.io/platform/current/clients/confluent-kafka-python/html/index.html)