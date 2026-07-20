# Standard Operating Procedure: Task Verification & Auditing (`work_verification.md`)

This document defines the official verification, audit, and quality assurance protocol for all agents and developers. It ensures systemic integrity, eliminates redundancies, and enforces strict execution standards by heavily cross-referencing the rules defined in `tasks.md` and `references.md`.

---

## 1. Architectural Verification Priorities

During any audit cycle, verification must be executed in the following hierarchical order of importance:

### I. AI & Core Services Validation
* **Inference Integrity:** Verify that LLM orchestration, model clients, and inference pipelines strictly adhere to the designated prompt structures and output schemas.
* **Evaluation & Guardrails:** Ensure that benchmarking tests (e.g., DeepEval, Ragas) and safety checks pass successfully for any new AI feature or agent logic.
* **Context & Token Optimization:** Confirm that context windows are managed efficiently, preventing state leakage and optimizing latency for all AI service calls.

### II. Backend & Infrastructure Validation
* **Data Schemas & Type Safety:** Validate that all backend models (e.g., Pydantic, SQLAlchemy) are strictly typed and accurately reflect the architectural intent.
* **System Resiliency:** Verify that API routes, message brokers, and database connection pools include proper error handling, retry logic, and timeout fallbacks.
* **Containerization & Deployment:** Ensure that Dockerfiles, container orchestrations, and dependency managers (e.g., `pyproject.toml`) successfully build the updated environment without introducing bloat.

---

## 2. Audit Trigger & Scope

**Trigger:** Verification is mandatory whenever a Subtask is marked as completed (`[x]`) in the active version `tasks/v<N>/sub/` directory.

1. **Identify the Target Perimeter:** Retrieve the active file changes or git commit diff associated with the completed Subtask.
2. **Isolate Execution Assets:** Focus strictly on the structural footprint (code, configurations, schemas) introduced by the `[x]` items.

---

## 3. Work Verification Flow

```mermaid
graph TD
    A[Start Audit: Parse Subtask] --> B[Locate Completed Items]
    B --> C[Verify Implementation via Code Diff]
    C --> D{Redundancies or DRY Violations?}
    D -- Yes --> E[Refactor Duplicate Logic to Shared Layer]
    D -- No --> F{Specification Discrepancies?}
    F -- Gaps Found --> G[Create Remedial Tasks / Fix Implementation]
    F -- No Gaps --> H[Run Verification Tests]
    H --> I{Are there Repeating Mistakes / Blockers?}
    I -- Yes --> J[Update work_principle.md / Log to references/issues/]
    I -- No --> K[Check Roll-Up: Are all sister subtasks done?]
    K -- Yes --> L[Mark Base Task Complete & End Audit]
    K -- No --> M[End Audit: Green Sign-off]
```

### Step 1: Trace Execution to Specification (Adhere to `tasks.md`)
* Map the active code changes directly to the "Definition of Done" outlined in the specific Subtask file.
* **Discrepancy Check:** Look for architectural gaps such as missing validation fields, improper log formats, or hardcoded variables that should be environment-driven.
* **Roll-Up Check:** If this is the final Subtask, verify all sister Subtasks are complete before marking the parent Base Task in `tasks/v<N>/base/` as `[x]`.

### Step 2: Redundancy & Core DRY Audit
* Scan for duplicate code patterns that violate modular workspace principles.
* If duplicate helper functions, AI clients, or data models are found, immediately refactor them into a centralized `common/` or shared reference package.
* Update all dependent submodules to import from this unified layer.

### Step 3: Test Execution
* Run the designated test suites. **All AI evaluation checks must pass first**, followed immediately by standard unit and integration tests for the backend logic and data layers.

### Step 4: Systemic Feedback Loop (Adhere to `references.md`)
* **Log Technical Debt:** If architectural flaws, deprecated dependencies, or technical blockers are discovered during the audit, log a detailed report in `references/issues/` so future execution agents are aware.
* **Evolve the Baseline:** If an architectural oversight or anti-pattern is repeatedly discovered (e.g., omitting telemetry hooks or failing to cache test environments), instantly update `prompts/work_principle.md` to prevent future occurrences and evolve the system's baseline intelligence.

---

## 4. Tool Reference Checklist

Below are the standard capabilities expected to be utilized during the verification flow:

| Audit Action | Target Action | Description / Operational Intent |
|---|---|---|
| **Trace Code & References** | Global Symbol Search | Scan for usage of environment fields, core AI clients, or specific models across the codebase to ensure consistency. |
| **Locate Checklist Progress** | Workspace Text Grep | Search the active `tasks/v<N>/` directory for updated `\[x\]` tags to map out the current verification perimeter. |
| **Static Code Inspection** | File Content Review | Review logic blocks for proper type definitions, Pydantic validations, and telemetry integration. |
| **Analyze Active Changes** | Version Control Diff | Execute diff commands (e.g., `git diff`) to isolate what changed in the workspace compared to the baseline branch. |
| **Verify Test Quality** | Environment Test Execution | Run the workspace's designated test frameworks to guarantee both AI reliability and backend system health. |