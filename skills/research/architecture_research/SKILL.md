---
version: 1.0.0
updated: 2026-08-27
id: skill_research_architecture_research
links:
  - ../web_research/SKILL.md
  - ../../../references/references.md
  - ../../../references/resource/
  - ../../../workflows/planning/planning.md
  - ../../../design/design.md
---

# Skill: Architecture & Design Pattern Research

> **Purpose**: A targeted research workflow for the design and architecture phase.
> Before finalising any HLD/LLD, search curated awesome-lists, GitHub trending,
> and primary sources to discover proven patterns, scalable designs, and
> high-quality reference implementations.
> This skill supplements — it does NOT replace — `research/web_research/SKILL.md`.

## Trigger Conditions

- Load this skill when entering **PLAN mode** for any non-trivial system design.
- Load when the user asks to research scalability patterns, architecture choices,
  technology stacks, or implementation approaches.
- Load when `../../../workflows/planning/planning.md` triggers the
  "Research First" phase (section 2).
- Load when `../../../workflows/planning/init_project.md` calls for
  open-source template ingestion.

---

## Curated Primary Sources

These are the **required starting points** for architecture research. Always
consult at least the relevant subset before proposing any HLD/LLD.

### 1. GitHub Awesome Lists Hub

**URL**: `https://github.com/topics/awesome`
**When to use**: Discovery — find the most relevant curated list for the
domain being researched (e.g. `awesome-python`, `awesome-scalability`,
`awesome-fastapi`, `awesome-machine-learning`, etc.).

Steps:
1. Search `https://github.com/topics/awesome` for the domain keyword.
2. Open the top 2-3 most starred, recently-updated awesome lists.
3. Scan the TOC for sections directly relevant to the current design question.
4. Extract: library names, GitHub links, brief descriptions.

### 2. Awesome Scalability

**URL**: `https://github.com/binhnguyennus/awesome-scalability`
**When to use**: Any time scalability, reliability, performance, distributed
systems, or system design is in scope.

Sections to prioritise:
- **Scalability** — horizontal vs vertical, load balancing, caching strategies.
- **Availability & Reliability** — circuit breakers, health checks, retries.
- **Stability & Resiliency** — bulkheads, rate limiting, graceful degradation.
- **Performance** — latency budgets, profiling, CDN, async patterns.
- **Database** — sharding, replication, CAP theorem trade-offs.
- **Messaging** — queues, event streaming (Kafka, SQS, RabbitMQ).
- **Microservices & Distributed Systems** — service mesh, API gateway patterns.

### 3. Awesome Python

**URL**: `https://github.com/vinta/awesome-python`
**When to use**: Any Python-based implementation. Look up canonical libraries
for the task category (web frameworks, async, databases, serialisation, testing,
CLI, ML, etc.).

Steps:
1. Navigate to the relevant section heading (e.g. `Web Frameworks`, `ORM`,
   `Async`, `CLI`, `Testing`).
2. Compare starred libraries — favour actively maintained, widely adopted ones.
3. Cross-reference with PyPI download stats and GitHub issue/PR activity.

### 4. GitHub Trending

**URL**: `https://github.com/trending`
**When to use**: Discover momentum — what is the community currently building,
adopting, or excited about in the relevant language/domain.

Filters to apply:
- Language filter: match the project stack (e.g. `Python`, `TypeScript`).
- Time filter: `weekly` and `monthly` views.
- Look for: repos solving a similar problem to what is being designed.
- Note: trending does not equal production-ready. Validate maturity before recommending.

### 5. GitHub Search — Specific Topics

Use targeted GitHub searches for direct prior art:

Useful search patterns:
- `topic:<framework>` — find ecosystem projects.
- `stars:>500 language:Python <keyword>` — quality filter.
- `pushed:>2024-01-01` — recency filter for active maintenance.
- URL pattern: `https://github.com/search?q=<topic>&sort=stars&type=repositories`
- URL pattern: `https://github.com/topics/<topic>`

---

## Research Process

### Step 1 — Frame the Design Question
- State the architectural decision or design question explicitly.
- Identify the domain (e.g. "real-time data pipeline", "REST API with auth",
  "ML inference service").
- Note constraints: language, cloud provider, latency SLA, scale targets.

### Step 2 — Awesome List Scan (Required)
1. Go to `https://github.com/topics/awesome` — find domain-specific list.
2. If scalability/performance is in scope — check `awesome-scalability`.
3. If Python is in stack — check `awesome-python`.
4. Extract 5-10 candidate patterns, libraries, or reference implementations.

### Step 3 — GitHub Trending Check
1. Go to `https://github.com/trending?l=<language>&since=weekly`.
2. Note any repos directly relevant to the design question.
3. Flag momentum items with a `[TRENDING]` marker in research notes.

### Step 4 — GitHub Primary Source Dive
- For each shortlisted pattern or library:
  1. Visit the GitHub repo directly.
  2. Read: `README`, `ARCHITECTURE.md` (if present), key issues/discussions.
  3. Check: star count, last commit date, open issues, recent releases.
  4. Assess: production readiness, community health, licence compatibility.

### Step 5 — Cross-Reference with Web Research
- For top candidates, run targeted web searches per `web_research/SKILL.md`
  sections 3-4 to find:
  - Official docs, changelogs, migration guides.
  - Performance benchmarks and production case studies.
  - Known failure modes, limitations, and anti-patterns.

### Step 6 — Synthesise & Recommend
- Produce a short comparison table: Pattern/Library | Pros | Cons | Stars | Updated
- Give a **concrete recommendation** with rationale.
- Note confidence: `confirmed` (validated in production), `likely`
  (well-adopted), `speculative` (trending / new).
- Save to `../../../references/resource/<domain>_architecture_research.md`.

---

## Constraints

- **Awesome lists are starting points, not ground truth** — always validate
  entries against their actual repos and docs.
- **GitHub stars do not equal quality** — cross-check with issue health, commit
  recency, and production usage evidence.
- **No single-source decisions** — at minimum use one awesome list + one
  primary repo + one external source.
- **Do not plagiarise** — summarise and cite; do not copy content verbatim.
- **Bias check** — flag any vendor-sponsored or commercially biased source.

---

## Expected Outputs

- A research notes file at `../../../references/resource/<domain>_architecture_research.md`:
  - Candidate patterns/libraries with cited sources.
  - Comparison table.
  - Recommendation with rationale and confidence level.
- Updated `../../../references/references.md` if any finding changes the HLD.
- Design decision record (ADR) in `../../../design/` if a major architectural
  choice is made.

---

## Quick Reference — Key URLs

| Source | URL | Use For |
|--------|-----|---------|
| Awesome Topics Hub | `https://github.com/topics/awesome` | Domain discovery |
| Awesome Scalability | `https://github.com/binhnguyennus/awesome-scalability` | Scale/perf/reliability patterns |
| Awesome Python | `https://github.com/vinta/awesome-python` | Python library selection |
| GitHub Trending (weekly) | `https://github.com/trending?since=weekly` | Momentum signals |
| GitHub Trending (Python) | `https://github.com/trending/python?since=weekly` | Python momentum |
| GitHub Search | `https://github.com/search?type=repositories&sort=stars` | Direct prior art |
