# Subtask: EvalOps CI/CD Pipeline

**Parent Link:** `base/evalops_qa.md`

## Actionable Steps
- [ ] Create `.github/workflows/evalops.yml` GitHub Actions workflow.
- [ ] Trigger on: PRs to `main` branch.
- [ ] Steps: Install deps (`poetry install --all-extras`), Lint (`ruff check .`, `mypy .`), Safety tests, Optional benchmark tests (on label).
- [ ] Maintain test fixtures in `tests/fixtures/`, version controlled, with documented JSON schemas.

## Dependencies
- GitHub repository configured. Poetry lock file up to date.

## Definition of Done
- PRs trigger CI automatically. Lint and safety tests pass. Results reported as PR check annotations.
