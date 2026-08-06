# Subtask: ST-08 — AI Agent Enrichment & Comparison Pipelines

**Parent Link:** [BT-03: Hybrid Search Engine & AI Agent Subsystem](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/base/BT-03_search_ai_engine.md)

## Objective
Build `services/ai-agent-service/` featuring an autonomous Profile Auto-Enrichment Agent (website scraper + amenity/staff ratio parser) and a dynamic Multi-Facility Comparative Summarizer Agent.

---

## Technical Actionable Steps

- [ ] **Step 1: Implement Profile Auto-Enrichment Agent**
  - Triggered upon facility creation or website claim.
  - Async web crawler extracts text from facility homepage, pricing, and care pages.
  - LLM chain parses amenities, nurse-to-resident ratios, visiting hours, and memory care capabilities.
  - Generates structured draft profile updates with source citations and confidence scores.

- [ ] **Step 2: Implement Multi-Facility Comparative Summarizer Agent**
  - Accepts 2 to 4 facility IDs and user preference context.
  - Pulls ratings, CMS penalties, staffing ratios, pricing, and amenities.
  - LLM generates structured markdown comparison: pros, cons, key differentiators, pricing breakdown, and regulatory inspection summaries.

- [ ] **Step 3: Integrate Verification Hook**
  - Route output through confidence threshold check before mutating live facility data.

---

## Dependencies
- Completion of `ST-03_taxonomy_and_common_packages.md` and `ST-07_search_hybrid_engine.md`.
- Frameworks: `langchain`, `llamaindex`, `pydantic`.

## Complexity Rating
**High**

## Definition of Done
- Auto-enrichment agent extracts amenity and pricing schemas from sample facility URLs.
- Comparison agent produces concise comparative summaries with zero hallucinated inspection scores.
