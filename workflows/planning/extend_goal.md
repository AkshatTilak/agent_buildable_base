---
version: 2.0.0
updated: 2026-08-20
id: extend_goal
links:
  - ../../tasks/tasks.md
  - ../../tasks/goal/goal.md
  - ../../design/design.md
  - ../../features/features.md
  - ../user/user_input.md
---

# Goal (SRS) Revision — CRUD, Versioning & Task Restructuring

> **Purpose**: The Goal file (`tasks/goal/goal.md`) is the project's **SRS**.
> Requirements evolve over the development journey. This workflow is the
> **single entry point** for creating, updating, deepening, and retiring
> requirements — and for propagating those changes into the DDS and the
> base/sub task tree. Do not edit the SRS outside this workflow.

## 1. When To Use

- A requirement is added, changed, clarified, or retired.
- The goal lacks depth (missing subsystems, edge cases, NFRs).
- A user decision changes scope (see `../user/user_input.md`).
- Verification or drift audits (`../quality/recheck_codebase.md`) reveal the
  task tree no longer matches the SRS.

## 2. SRS CRUD Operations

Requirement IDs (`FR-###`, `NFR-###`, `IR-###`) are **stable and never reused**.

| Operation | How |
|-----------|-----|
| **Create** | Append the next free ID in the correct section (`FR`, `NFR`, `IR`). |
| **Update** | Edit the requirement text in place; keep the ID; note the change in the Changelog. |
| **Deepen** | Split a vague requirement into sub-requirements using dotted IDs (`FR-003.1`, `FR-003.2`) under the parent. |
| **Retire** | Strike through (`~~FR-007~~ — retired in vX.Y.Z: reason`); never delete or reuse the ID. |

## 3. Versioning The SRS

Bump the `version` frontmatter field on **every** edit:

| Bump | When |
|------|------|
| **Patch** (x.y.**z**) | Wording/clarity fixes; no requirement meaning changes. |
| **Minor** (x.**y**.0) | Requirements added or deepened; existing task mappings stay valid. |
| **Major** (**x**.0.0) | Requirements updated/retired such that existing Base/Sub tasks must be restructured. |

Every bump records what changed and why in the SRS Changelog (§9).

## 4. Impact Analysis (Required For Minor/Major Bumps)

1. **Diff the requirement IDs**: which IDs were added, updated, deepened, or retired?
2. **Find affected tasks**: search `tasks/base/` and `tasks/sub/` for matching
   `srs_refs` frontmatter entries.
3. **Check the DDS**: identify `design/` and `features/` docs linked to the
   affected requirements and tasks.
4. **Classify the restructure** per affected task:
   - **Add**: new Base/Sub tasks for new requirements (assign `srs_refs`).
   - **Improve**: update objectives, steps, edge cases, or DoD of existing tasks.
   - **Remove**: supersede obsolete tasks — set `status: blocked` and link
     `supersedes:` from the replacement; never silently delete history.
   - **Renumber**: apply the renumbering rule (`../../tasks/tasks.md` §6) so
     `sub/` prefixes stay ordered and gap-free; update all `depends_on`.

## 5. Restructuring Procedure

1. **DDS first**: update affected `design/` (HLD/LLD) and `features/` docs so
   intent is correct before touching tasks.
2. **Restructure tasks**: add/improve/remove/renumber per §4. Keep every Base
   Task's `srs_refs` accurate.
3. **Sync registries**: update the SRS Base Task Registry (§7) and each Base
   Task's Subtask Registry.
4. **Bump & log**: bump `version` + `updated` on every touched file; record
   changelog entries (SRS §9, per-task Changelog, per-feature CHANGELOG).
5. **Record user decisions**: log scope decisions in SRS §8.

## 6. Depth Checklist (For Deepening)

- Are all major subsystems covered by at least one requirement?
- Are edge cases and exception paths reflected in requirements or task DoDs?
- Are non-functional requirements (performance, security, observability)
  addressed in §4 of the SRS?
- Are external interfaces specified in §5 of the SRS?
- Are user decisions recorded?

## 7. Output

- Updated, version-bumped SRS (`tasks/goal/goal.md`) with changelog entry.
- Updated DDS docs (`design/`, `features/`) reflecting the new intent.
- Restructured Base/Sub tasks with accurate `srs_refs`, registries, and
  `depends_on` graphs.

## 8. Changelog

- `2.0.0` (2026-08-20): Rewritten as the SRS CRUD & revision workflow with
  versioning rules, impact analysis, and task restructuring procedure.
- `1.0.0` (2026-08-18): Initial extend-goal workflow.
