# Subtask: ST-09 — Human-in-the-Loop Verification Queue

**Parent Link:** [BT-03: Hybrid Search Engine & AI Agent Subsystem](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/base/BT-03_search_ai_engine.md)

## Objective
Build the Human-in-the-Loop verification engine to intercept low-confidence AI extraction outputs (< 0.85 certainty threshold), queue them for `platform_data_admin` review, and manage publishing approvals.

---

## Technical Actionable Steps

- [ ] **Step 1: Implement Confidence Score Threshold Guardrail**
  - Implement validation wrapper over all `ai-agent-service` and Tier 3 OCR outputs.
  - If overall score < 0.85, set status `pending_human_verification` and write item to `staged_ai_outputs`.

- [ ] **Step 2: Build Verification Review APIs**
  - `GET /admin/verification-queue`: List pending AI outputs with diff highlighting (extracted value vs. web source snippet).
  - `POST /admin/verification-queue/{id}/approve`: Apply edited/approved data to active facility profile.
  - `POST /admin/verification-queue/{id}/reject`: Discard extraction with reason.

- [ ] **Step 3: Build Telemetry & Model Feedback Loop**
  - Record human corrections to feed into agent evaluation datasets for continuous prompt tuning.

---

## Dependencies
- Completion of `ST-02_auth_and_rbac_service.md` and `ST-08_ai_agent_enrichment_comparison.md`.

## Complexity Rating
**Medium**

## Definition of Done
- Any AI output with confidence < 0.85 is intercepted and blocked from live DB updates.
- Data admin approval via API successfully publishes corrected fields to core facility profiles.
