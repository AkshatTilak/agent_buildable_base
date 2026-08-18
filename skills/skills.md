---
version: 1.2.0
updated: 2026-08-18
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
- Register new skills in the index below.
- Use `../workflows/user/find_skills.md` to discover skills online or from hubs.
- Use `manage_skills.md` for create/update/delete operations.

## 3. Index

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
| Backend — API design | `backend/api_design/SKILL.md` | REST and GraphQL API design principles. |
| Backend — domain modeling | `backend/domain_modeling/SKILL.md` | Ubiquitous language, domain glossary, and ADRs. |

