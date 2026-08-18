---
version: 1.0.0
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
| UI/UX creation | `ui/creation/SKILL.md` | Design and build user interfaces. |
| Traceback check | `debug/traceback/SKILL.md` | Diagnose and fix tracebacks/errors. |

