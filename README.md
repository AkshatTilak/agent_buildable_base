# Agentic Workspace: Autonomous System Builder

Welcome to the Agentic Workspace. This repository is an AI-driven, highly modular framework designed to autonomously architect, execute, and verify complete software systems from a raw user idea. 

---

## 1. Core Architecture & AI Services

This system is built with AI orchestration as a first-class citizen. The intelligence layer drives all downstream execution.

### Agent Orchestration Layer
* **High-Level System Architect (`agent.md`):** The primary orchestrator. It digests requirements, maps the high-level topology, and bifurcates the work into actionable milestones.
* **Execution & Inference Agents:** Specialized agents that read isolated tasks, fetch necessary context, and write the core business logic.
* **Verification & Guardrail Agents:** Autonomous auditing agents that validate inference integrity, token efficiency, and code structure before marking a task complete.

### AI Integration & Observability
* **Inference Integrity:** Built-in hooks for LLM configurations, prompt management, and evaluation benchmarks.
* **Contextual Memory (`references/`):** A decoupled memory bank that prevents context window pollution. 

---

## 2. Backend & Infrastructure Foundations

Supporting the AI orchestration layer is a robust, production-ready backend designed for scalability.
* **Modular Subsystems:** Shared utilities, data models, and configurations live in a centralized library to enforce DRY principles.
* **Data & Type Safety:** Heavy utilization of explicit typing and Pydantic validation.
* **Containerization & Deployment:** Native support for isolated Docker environments.

---

## 3. Workspace Directory Structure

```text
├── prompts/                # Core instructions and behavioral guardrails
│   ├── work_principle.md   # SOP for task selection, execution, and coding standards
│   └── work_verification.md# SOP for auditing, AI evaluation, and regression testing
├── references/             # The contextual memory bank for agents
│   ├── references.md       # 📖 Master guide for reading/writing contextual memory
│   ├── code/               # Explanations of complex logic and historical mappings
│   ├── deployment/         # Dockerfiles, CI/CD, and environment templates
│   ├── issues/             # Technical debt, bottlenecks, and blocked tasks
│   ├── logic/              # Business rules and system interaction flows
│   ├── resource/           # External API docs, dataset links, and asset catalogs
│   ├── structure/          # Database schemas, API routing, and dependency graphs
│   └── user/               # Human-in-the-loop requests (API keys, manual QA)
├── tasks/                  # Active execution directory
│   ├── tasks.md            # 📖 Master guide for task hierarchy and state roll-ups
│   ├── goal/               # The ultimate system objective
│   ├── base/               # Broad architectural milestones (The "What" and "Why")
│   ├── sub/                # Granular execution units (The "How")
│   └── temp/               # Holding zone for spontaneous, out-of-scope issues
├── agent.md                # The master System Architect prompt and bootstrap guide
└── README.md               # You are here