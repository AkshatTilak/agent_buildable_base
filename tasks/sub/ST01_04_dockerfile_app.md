# ST01_04: Finalize Multi-Stage Dockerfile

## Parent Link
`base/BT01_infrastructure_setup.md`

## Actionable Steps
- [ ] Step 1: Test Poetry install layer caching (modify app code, rebuild — deps should be cached)
- [ ] Step 2: Verify non-root user (echomind) runs the application
- [ ] Step 3: Validate health check endpoint responds from within the container
- [ ] Step 4: Test the app container connects to postgres, redis, kafka via Docker network
- [ ] Step 5: Verify upload volume mount works correctly

## Dependencies
- `pyproject.toml` must have correct dependency specifications
- All core services must be running

## Definition of Done
App container builds successfully, runs as non-root, connects to all services, and health check passes.
