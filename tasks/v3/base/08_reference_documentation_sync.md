# Base Task: Reference & Documentation Sync for V3

## Objective
Update all project reference documents, the root agent.md, the buildable base agent.md, the README.md, and deployment guides to accurately reflect the V3 state of the platform.

## Business/System Value
References are the memory bank for AI agents and developers. Stale documentation leads to agents writing code that contradicts current architecture, missing established patterns, or re-implementing existing features. Every version transition requires a documentation pass.

## Complexity Rating
Low (Documentation-only, no code changes).

## Subtask Registry
* `[ ] sub/docs_agent_md_update.md` — Update both `agent.md` files (root + buildable base) with V2 completion summary and V3 goals.
* `[ ] sub/docs_readme_update.md` — Update README.md with V3 changelog, new Docker profile commands, RAM requirements table.
* `[ ] sub/docs_frontend_reference_update.md` — Update `references/structure/frontend.md` with V3 component architecture, routing, new store slices.
* `[ ] sub/docs_deployment_reference_update.md` — Update `references/deployment/` with new Docker profiles and RAM-optimized commands.
* `[ ] sub/docs_master_task_registry_update.md` — Update `tasks/tasks.md` master registry to include V3 row with status and description.
