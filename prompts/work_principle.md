# Standard Operating Procedure: Task Execution & Principles (`work_principle.md`)

This document outlines the strict protocol and guidelines for all agents and developers when selecting, executing, integrating, and documenting tasks within this workspace.

---

## 1. Task Selection Limits & Prioritization

- **Max Active Load:** You may select and work on **no more than 3 tasks** simultaneously to maintain deep context and execution quality.
- **Dependency First Prioritization:** Work must be sequenced to unblock the most critical system components first:
  1. **AI & Core Services:** Prioritize AI model integrations, prompt engineering structures, inference pipelines, and core agent architectures first.
  2. **Backend & Infrastructure:** Following AI services, prioritize foundational backend tasks (e.g., database schemas, shared utilities, Docker setups, and message brokers).
  3. **Upstream Submodules:** Only after the AI and backend foundations are stable should work begin on downstream dependencies or UI wrappers.

---

## 2. Core Execution Principles

### Context Before Code (Adhere to `references.md`)
- **Query Memory First:** Before implementing complex logic, you **must** check the `references/` directory (e.g., `references/logic/` or `references/structure/`) to ensure your implementation aligns with the Architect's systemic vision.
- **Update the Bank:** If you make a significant design choice, immediately document it in the appropriate `references/` subfolder.

### Complete & Integrated Delivery
- Every task must be completed **fully and end-to-end**, integrating across all dependent submodules.
- **Never implement duplicate code.** Abstract recurring logic into a shared utility or `common/` package.
- **Run any inference needed to test.** Run and test any infrastructure that is needed(like docker, kafka, kubernetes.) Whatever is used.
---

## 3. Scope Management & Tracking (Adhere to `tasks.md`)

### Out-of-Scope Issues & The `temp/` Directory
If you encounter a bug, missing dependency, or design flaw that falls **beyond the scope of your current Subtask**:
  1. Do **NOT** attempt to resolve it silently.
  2. Following `tasks.md`, create a temporary markdown log in `tasks/temp/` to track the issue.
  3. Immediately return focus solely to your assigned Subtask.

### Status Roll-Ups
Always update the status of checklist items (`[ ]` to `[/]` to `[x]`). Remember the Roll-Up Rule: A Base Task is only complete when all of its linked Subtasks are verified and marked `[x]`.

## 4. Quality, Design, & Maintainability

### Standard Architectural Practices
- **Design Patterns:** Adhere to strictly typed, modern design patterns appropriate for the project's language (e.g., explicit typing and validation using Pydantic/dataclasses for Python).
- **Observability First:** Ensure proper logging, telemetry, and transaction tracing are integrated natively into all new features.
- **Testing Requirements:** Write tests concurrently with feature development. Prioritize evaluation benchmarks and safety checks for AI/inference pipelines, followed by comprehensive unit and integration tests for the backend logic.
- **Containerization:** Utilize Docker (or the project's equivalent containerization strategy) to spin up isolated environments for databases, testing, or service dependencies.
- **Dependency Management:** Always verify the project's dependency files (e.g., `pyproject.toml`, `package.json`) when adding new packages. Ensure they are assigned to the correct deployment or development groups.

### Clarity & Collaboration
- **Stop and Ask:** If a requirement, contract, or design decision is ambiguous, presents an architectural flaw, or lacks logical sense, **stop and ask for clarification** before making assumptions.
- **Documentation Updates:** If you introduce new configurations, architectural improvements, or setup flags, immediately **update the project-level README** so other agents and developers are aware.
- **Environment Variables:** If valid environment variables, API keys, or custom `.env` parameters are required to test or execute any module, explicitly prompt for these values.

### Documentation & Version Verification
- **Verify Before Coding:** Before modifying or adding code, consult the official online documentation of the respective library to ensure implementation aligns with modern best practices.
- **Strict Versioning:** Always cross-reference your implementation with the exact library versions defined in the project's dependency manager to prevent deprecation errors or version conflicts.
- **Official Documentation Sources:** Refer to these resources for packages listed in `pyproject.toml`:
  - **Hugging Face & ML:** [Hugging Face Docs](https://huggingface.co/docs), [Transformers Docs](https://huggingface.co/docs/transformers/index), [Sentence Transformers](https://sbert.net/)
  - **LLM Orchestration & Clients:** [LangChain Docs](https://python.langchain.com/v0.3/docs/introduction/), [LangGraph Docs](https://langchain-ai.github.io/langgraph/), [LiteLLM Docs](https://docs.litellm.ai/)
  - **API & Data Validation:** [FastAPI Docs](https://fastapi.tiangolo.com/), [Pydantic v2 Docs](https://docs.pydantic.dev/latest/)
  - **Database & Storage:** [SQLAlchemy v2 Docs](https://docs.sqlalchemy.org/en/20/), [Qdrant Docs](https://qdrant.tech/documentation/), [Neo4j Python Driver](https://neo4j.com/docs/python-manual/current/)
  - **Evaluation & MLOps:** [DeepEval Docs](https://docs.confident-ai.com/), [Ragas Docs](https://docs.ragas.io/en/stable/), [MLflow Docs](https://mlflow.org/docs/latest/index.html)
  - **Other Integrations:** [Model Context Protocol (MCP)](https://modelcontextprotocol.io/), [Llama.cpp Python](https://abetlen.github.io/llama-cpp-python/), [Confluent Kafka Python](https://docs.confluent.io/platform/current/clients/confluent-kafka-python/html/index.html)