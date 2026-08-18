---
version: 2.0.0
updated: 2026-08-18
id: find_skills
links:
  - ../../skills/skills.md
  - ../../skills/manage_skills.md
  - ../planning/planning.md
  - ../user/user_input.md
---

# Find Skills — Discover Skills Online & From Hubs

> **Purpose**: Search online and in skill hubs for skills for a specific need,
> evaluate them, and add them to the `skills/` library. Integrates with existing
> skill ecosystems.

## 1. When To Use

- When a task needs specialized knowledge not covered by existing skills.
- When the user asks to find a skill for a specific job.

## 2. Skill Hubs & Sources

The following hubs are known to be viable for discovering skills:

- **agentskillshub.dev** — 1213+ security-scored skills, MCP servers, and Claude
  Code skills. Each entry has a security grade (A–F), install guidance, and
  compatibility context. Skills installable via `claude mcp add` or `npx`.
- **aihero.dev/skills** (Matt Pocock) — a practical skill system. Installable via
  `npx skills@latest add <owner>/<repo>` or `claude plugins install`. Uses a
  `SKILL.md`-style format.
- **GitHub** — search topics like `agent-skills`, `claude-skills`, `mcp-skills`.

> **skillhub.club** is not viable (dead/redirects to ads) and is not used.

## 3. Process

1. **Clarify** the need (see `../user/user_input.md`).
2. **Search** the hubs above and the web for candidate skills (see
   `../planning/planning.md`).
3. **Evaluate** candidates: relevance, accuracy, security (prefer A/B grades on
   agentskillshub), and fit for the project's stack and environment.
4. **Pull** the chosen skill source into `skills/_staging/`:
   - Clone the repository or download the skill files.
   - Preserve the original structure for reference.
   - Do NOT modify staged files — they are read-only reference.
5. **Adapt** the skill into our format:
   - Create `skills/<domain>/<skill>/SKILL.md` + optional `scripts/`.
   - Convert frontmatter to our schema (`version`, `updated`, `id`, `links`).
   - Rewrite instructions to reference our internal files (`workflows/`,
     `references/`, `CONVENTIONS.md`, etc.).
   - Remove hub-specific setup instructions.
   - Add our standard sections: Trigger Conditions, Steps, Expected Outputs,
     References.
   - Credit the original source in References.
6. **Register** it in the skills index (`../../skills/skills.md`).
7. **Delete** the staged source from `skills/_staging/` after verification.
8. Use `../../skills/manage_skills.md` for create/update/delete operations.

## 4. Skill Format

Each skill is a folder with:
- **`SKILL.md`** (required): metadata + instructions (trigger conditions, steps,
  expected outputs, references).
- **`scripts/`** (optional): executable code that supports the skill.

## 5. Staging Directory

`skills/_staging/` is a temporary holding area for pulled skill sources during
the import process. It should always be empty except during active import work.

## 6. Output

- A new skill folder in `../../skills/`.
- Updated skills index.
- Empty `_staging/` directory.
