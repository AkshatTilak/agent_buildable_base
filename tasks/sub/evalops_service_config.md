# Subtask: EvalOps Service Configuration

**Parent Link:** `base/evalops_qa.md`

## Actionable Steps
- [ ] Ensure EvalOps is configured as a QA/CI tool, not a production service.
- [ ] In docker-compose: EvalOps runner behind `test` profile.
- [ ] Kafka consumers only start when explicitly enabled (not on default gateway startup).
- [ ] Document requirement: `poetry install --extras "evalops"`.

## Dependencies
- docker-compose configuration. `pyproject.toml` extras groups.

## Definition of Done
- EvalOps does not start with default gateway. Test profile works. Documentation updated.
