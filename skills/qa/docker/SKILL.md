---
version: 1.0.0
updated: 2026-08-18
id: skill_qa_docker
links:
  - ../../../references/deployment/optimization_checklist.md
  - ../../../workflows/execution/work_verification.md
---

# Skill: QA — Docker

## Trigger Conditions
- Load this skill when auditing or testing Docker builds.
- Load when verifying Dockerfiles, images, or container orchestration.

## Steps
1. **Verify the build** succeeds.
2. **Apply the deployment optimization checklist** (see
   `../../../references/deployment/optimization_checklist.md`): multi-stage
   builds, minimal base images, cache cleanup after install, `.dockerignore`,
   layer ordering.
3. **Verify image size** is not bloated.
4. **Verify runtime**: containers start, logs are captured (see
   `../../../references/logs/`), and services are reachable.

## Expected Outputs
- Docker build verified and optimized.
- Image size confirmed reasonable.
- Containers run correctly with logs captured.

## References
- `../../../references/deployment/optimization_checklist.md`
- `../../../references/logs/logs.md`
