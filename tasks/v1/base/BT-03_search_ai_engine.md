# Base Task: BT-03 — Hybrid Search Engine & AI Agent Subsystem

## Objective
Implement the 3-way hybrid search microservice (`search-service`), autonomous LLM enrichment and comparison pipelines (`ai-agent-service`), human-in-the-loop review queue, and CRM lead routing / compliance billing microservices (`leads-service` & `billing-service`).

## Business / System Value
Delivers natural-language semantic discovery for families, automated facility profile generation, comparative AI analysis, and Stark/AKS compliant monetization and lead routing.

---

## Subtask Registry

- [ ] [ST-07: Hybrid Search Subsystem Implementation](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/sub/ST-07_search_hybrid_engine.md)
  - Details: PostGIS spatial queries, OpenSearch/Postgres BM25 text search, `pgvector` embedding generator, and Reciprocal Rank Fusion (RRF).
- [ ] [ST-08: AI Agent Enrichment & Comparison Pipelines](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/sub/ST-08_ai_agent_enrichment_comparison.md)
  - Details: Profile auto-enricher agent (web scraper + structured extractor) and multi-facility comparative summary agent.
- [ ] [ST-09: Human-in-the-Loop Verification Queue](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/sub/ST-09_human_in_loop_review_queue.md)
  - Details: Confidence score evaluator (< 0.85 threshold flagger), `platform_data_admin` review endpoints, and auto-publish workflows.
- [ ] [ST-10: Leads & AKS-Compliant Billing Services](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/sub/ST-10_leads_and_billing_service.md)
  - Details: `leads-service` CRM kanban, tour booking engine, and `billing-service` with Stark/Anti-Kickback Statute compliance firewall.

---

## Complexity Rating
**High** — Involves spatial/vector hybrid search indexing, async LLM agent workflows, multi-service event queues, and legal compliance routing.

## Acceptance Criteria
- `search-service` returns relevant facilities ranked by spatial distance, text keywords, and semantic query vectors using RRF.
- Auto-enrichment agent automatically populates draft amenities and care services upon facility claim.
- Dynamic comparison agent synthesizes side-by-side pros, cons, pricing, and citations for 2+ facilities.
- AI outputs below 0.85 confidence score are routed to the verification queue and prevented from live publishing until approved.
- Leads are safely routed according to AKS rules without illegal pay-per-referral violations.
