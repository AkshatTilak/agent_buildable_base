---
version: 1.0.0
updated: 2026-08-18
id: skill_manage_skills
links:
  - skills.md
  - ../workflows/user/find_skills.md
---

# Skill: Manage Skills — Create, Update, Delete

> **Purpose**: A meta-skill for managing the skills library itself. Handles
> creating, updating, and deleting skills in the `skills/` folder.

## Trigger Conditions
- Load this skill when creating, updating, or deleting a skill.
- Load when the user asks to add, change, or remove a skill.

## 1. Create A Skill

1. **Clarify** the need (see `../workflows/user/user_input.md`).
2. **Choose** the domain folder: `qa/`, `ui/`, `debug/`, or a new domain.
3. **Create** `skills/<domain>/<skill>/SKILL.md` with:
   - Frontmatter (`version`, `updated`, `id`, `links`).
   - **Trigger conditions**: when to load this skill.
   - **Steps**: the procedure to follow.
   - **Expected outputs**: what a correct result looks like.
   - **References**: links to sources.
4. **Add** an optional `scripts/` folder with executable code.
5. **Register** the skill in `skills.md` index.
6. **Bump** the skills index version.

## 2. Update A Skill

1. **Locate** the skill folder.
2. **Edit** `SKILL.md` (and `scripts/` if needed).
3. **Bump** the `version` in the frontmatter.
4. **Update** the `skills.md` index if the description/path changed.
5. **Note** the change in the skill's changelog (if present).

## 3. Delete A Skill

1. **Confirm** with the user before deleting (see
   `../workflows/user/user_input.md`).
2. **Remove** the skill folder.
3. **Remove** the entry from the `skills.md` index.
4. **Bump** the skills index version.

## 4. Import From A Hub

- Use `../workflows/user/find_skills.md` to discover skills from hubs
  (agentskillshub.dev, aihero.dev) and import them into our format.

## Expected Outputs
- Skills library stays consistent and indexed.
- Every skill has a valid `SKILL.md` and optional `scripts/`.
- The `skills.md` index is always up to date.

## References
- `skills.md`
- `../workflows/user/find_skills.md`
