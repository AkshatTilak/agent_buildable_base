# Governance Mode Workflow

Governance mode focuses on maintaining and evolving the Agent Buildable Base (ABB) specifications and rules rather than implementing project features or planning individual tasks.

## Responsibilities & Scope
1. **Meta-Specification Updates**: Maintain and update ABB root contracts:
   - `STACK.md` (runtime toolchain, verification manifest)
   - `agent.md` (root persona, operational modes)
   - `features/` (feature registry and specs)
   - `references/` (architectural and domain reference documents)
   - `skills/` (reusable workflows and skill documentation)
   - `workflows/` (execution and planning workflows)
   - `CONVENTIONS.md`, `CODING_PHILOSOPHY.md`, `USER_PREFERENCES.md`
2. **Version Bump & Changelog Discipline**:
   - Always increment frontmatter `version` when modifying specifications.
   - Record change rationale under `## Changelog`.
3. **Write Boundaries**:
   - Modifications to `tasks/` or `design/` are reserved for Plan mode.
   - Modifications to project source code are reserved for Agent mode.
   - Governance mode strictly enforces these meta-specification boundaries.
