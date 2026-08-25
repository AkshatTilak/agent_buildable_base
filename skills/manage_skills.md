---
version: 2.1.0
updated: 2026-08-25
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
2. **Choose** the domain folder: `qa/`, `ui/`, `debug/`, `practice/`,
   `research/`, `backend/`, or a new domain.
3. **Create** `skills/<domain>/<skill>/SKILL.md` with:
   - Frontmatter (`version`, `updated`, `id`, `links`).
   - **Trigger conditions**: when to load this skill.
   - **Steps**: the procedure to follow.
   - **Expected outputs**: what a correct result looks like.
   - **References**: links to sources.
4. **Add** an optional `scripts/` folder with executable code.
5. **Register** the skill in `skills.md` index in both the Human Table and the Machine YAML index (A4 format: `name`, `path`, `description`, `version`, `aliases`).
6. **Bump** the skills index version.

## 2. Update A Skill

1. **Locate** the skill folder.
2. **Edit** `SKILL.md` (and `scripts/` if needed).
3. **Bump** the `version` in the frontmatter.
4. **Update** the `skills.md` index (both human table and machine YAML block) if the description/path/version changed.
5. **Note** the change in the skill's changelog (if present).

## 3. Delete A Skill

1. **Confirm** with the user before deleting (see
   `../workflows/user/user_input.md`).
2. **Remove** the skill folder.
3. **Remove** the entry from both the human table and machine YAML block in `skills.md`.
4. **Bump** the skills index version.

## 4. Import From A Hub (Pull-Adapt-Delete)

When importing a skill from an external hub or repository, follow the
**pull-adapt-delete** workflow:

### Step 1: Pull
- Clone or download the external skill source into `skills/_staging/`.
- Preserve the original file structure for reference.
- Do NOT modify files in `_staging/` — they are read-only reference material.

### Step 2: Adapt
- Read the staged source material thoroughly.
- Create the new skill in the proper `skills/<domain>/<skill>/` location.
- Adapt the content to our format:
  - Convert frontmatter to our schema (`version`, `updated`, `id`, `links`).
  - Rewrite instructions to reference our internal files (`workflows/`,
    `references/`, `CONVENTIONS.md`, etc.) instead of external ones.
  - Remove hub-specific setup instructions (e.g. `/setup-matt-pocock-skills`).
  - Add our standard sections: Trigger Conditions, Steps, Expected Outputs,
    References.
  - Credit the original source in the References section.
- Ensure the adapted skill is self-contained and follows our conventions.

### Step 3: Delete
- After the adapted skill is verified and registered, delete the staged source
  from `skills/_staging/`.
- The `_staging/` directory should always be empty except during active
  import work.
- Note: Automated `SkillStagingHook` blocks edits to `skills.md` if `_staging/` is not purged.

### Verification Checklist
- [ ] Staged source pulled to `skills/_staging/`
- [ ] Adapted skill created in proper domain folder
- [ ] Frontmatter matches our schema
- [ ] Internal links updated to point to our files
- [ ] Hub-specific instructions removed
- [ ] Original source credited in References
- [ ] Skill registered in `skills.md` (Human table and Machine YAML index)
- [ ] Staged source deleted from `_staging/`

## Expected Outputs
- Skills library stays consistent and dual-format indexed.
- Every skill has a valid `SKILL.md` and optional `scripts/`.
- The `skills.md` index is always up to date.
- `_staging/` is empty except during active imports.

## References
- `skills.md`
- `../workflows/user/find_skills.md`
