---
version: 1.4.0
updated: 2026-08-27
id: skills
links:
  - ../workflows/user/find_skills.md
  - manage_skills.md
---

# Skills — Reusable Job Skills

> **Purpose**: A library of reusable skills for specific jobs. Each skill is a
> **folder** containing a `SKILL.md` (metadata + instructions) and an optional
> `scripts/` folder (executable code). Skills are **tracked in git** (unlike
> `assets/`, which is gitignored).

## 1. Skill Format

Each skill is a folder:

```
my-skill/
├── SKILL.md          # Required: metadata + instructions
└── scripts/          # Optional: executable code
```

`SKILL.md` contains:
- **Frontmatter**: `version`, `updated`, `id`, `links`.
- **Trigger conditions**: when to load this skill.
- **Steps**: the procedure to follow.
- **Expected outputs**: what a correct result looks like.
- **References**: links to sources.

## 2. How To Write A Skill

- One skill per folder, grouped by domain (`qa/`, `ui/`, `debug/`, ...).
- Self-contained: an agent should be able to follow it without other context.
- Register new skills in the index below (both the human table and machine YAML block).
- Use `../workflows/user/find_skills.md` to discover skills online or from hubs.
- Use `manage_skills.md` for create/update/delete operations.

## 3. Index

### Human Table
| Skill | Path | Purpose |
|-------|------|---------|
| Manage skills | `manage_skills.md` | Create/update/delete skills (CRUD). |
| QA — backend | `qa/backend/SKILL.md` | Quality assurance for backend systems. |
| QA — frontend | `qa/frontend/SKILL.md` | Quality assurance for frontend systems. |
| QA — docker | `qa/docker/SKILL.md` | Quality assurance for Docker builds. |
| QA — network | `qa/network/SKILL.md` | Quality assurance for networking. |
| QA — E2E | `qa/e2e/SKILL.md` | Live browser-driven E2E testing with Playwright. |
| UI/UX creation | `ui/creation/SKILL.md` | Design and build user interfaces. |
| UI — frontend design | `ui/frontend_design/SKILL.md` | Production-quality frontend design; avoids generic AI aesthetics. |
| Traceback check | `debug/traceback/SKILL.md` | Diagnose and fix tracebacks/errors. |
| Practice — TDD | `practice/tdd/SKILL.md` | Red-green-refactor test-first development cycle. |
| Practice — systematic debugging | `practice/systematic_debugging/SKILL.md` | Structured 7-phase debugging workflow. |
| Practice — code review | `practice/code_review/SKILL.md` | Structured code review against standards and spec. |
| Practice — verification before completion | `practice/verification_before_completion/SKILL.md` | Mandatory pre-completion verification gate. |
| Research — web research | `research/web_research/SKILL.md` | Structured web research with primary-source citations. |
| Research — architecture & design | `research/architecture_research/SKILL.md` | Curated awesome-lists & GitHub trending for HLD/LLD design research. |
| Backend — API design | `backend/api_design/SKILL.md` | REST and GraphQL API design principles. |
| Backend — domain modeling | `backend/domain_modeling/SKILL.md` | Ubiquitous language, domain glossary, and ADRs. |

### Machine Index (A4)
```yaml
skills:
  - name: manage_skills
    path: manage_skills.md
    description: Create/update/delete skills (CRUD).
    version: 2.0.0
    aliases: ["manage-skills", "manage"]
  - name: qa_backend
    path: qa/backend/SKILL.md
    description: Quality assurance for backend systems.
    version: 1.0.0
    aliases: ["qa/backend", "qa-backend"]
  - name: qa_frontend
    path: qa/frontend/SKILL.md
    description: Quality assurance for frontend systems.
    version: 1.0.0
    aliases: ["qa/frontend", "qa-frontend"]
  - name: qa_docker
    path: qa/docker/SKILL.md
    description: Quality assurance for Docker builds.
    version: 1.0.0
    aliases: ["qa/docker", "qa-docker"]
  - name: qa_network
    path: qa/network/SKILL.md
    description: Quality assurance for networking.
    version: 1.0.0
    aliases: ["qa/network", "qa-network"]
  - name: qa_e2e
    path: qa/e2e/SKILL.md
    description: Live browser-driven E2E testing with Playwright.
    version: 1.0.0
    aliases: ["qa/e2e", "qa-e2e", "e2e"]
  - name: ui_creation
    path: ui/creation/SKILL.md
    description: Design and build user interfaces.
    version: 1.0.0
    aliases: ["ui/creation", "ui-creation"]
  - name: ui_frontend_design
    path: ui/frontend_design/SKILL.md
    description: Production-quality frontend design; avoids generic AI aesthetics.
    version: 1.0.0
    aliases: ["ui/frontend_design", "frontend_design", "frontend-design"]
  - name: traceback_check
    path: debug/traceback/SKILL.md
    description: Diagnose and fix tracebacks/errors.
    version: 1.0.0
    aliases: ["debug/traceback", "traceback"]
  - name: practice_tdd
    path: practice/tdd/SKILL.md
    description: Red-green-refactor test-first development cycle.
    version: 1.0.0
    aliases: ["practice/tdd", "tdd"]
  - name: practice_systematic_debugging
    path: practice/systematic_debugging/SKILL.md
    description: Structured 7-phase debugging workflow.
    version: 1.0.0
    aliases: ["practice/systematic_debugging", "systematic_debugging", "debugging"]
  - name: practice_code_review
    path: practice/code_review/SKILL.md
    description: Structured code review against standards and spec.
    version: 1.0.0
    aliases: ["practice/code_review", "code_review", "review-code"]
  - name: practice_verification_before_completion
    path: practice/verification_before_completion/SKILL.md
    description: Mandatory pre-completion verification gate.
    version: 1.0.0
    aliases: ["practice/verification_before_completion", "verification"]
  - name: research_web_research
    path: research/web_research/SKILL.md
    description: Structured web research with primary-source citations.
    version: 2.1.0
    aliases: ["research/web_research", "web_research"]
  - name: research_architecture_research
    path: research/architecture_research/SKILL.md
    description: Curated awesome-lists & GitHub trending for HLD/LLD design research.
    version: 1.0.0
    aliases: ["research/architecture_research", "architecture_research", "design_research"]
  - name: backend_api_design
    path: backend/api_design/SKILL.md
    description: REST and GraphQL API design principles.
    version: 1.0.0
    aliases: ["backend/api_design", "api_design"]
  - name: backend_domain_modeling
    path: backend/domain_modeling/SKILL.md
    description: Ubiquitous language, domain glossary, and ADRs.
    version: 1.0.0
    aliases: ["backend/domain_modeling", "domain_modeling"]
```
