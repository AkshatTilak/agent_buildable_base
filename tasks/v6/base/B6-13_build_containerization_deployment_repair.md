# B6-13: Build, Containerization & Deployment Repair

> **Status:** `[x]`  
> **Owner:** `infrastructure`, `gateway`, `inference`, `agent_buildable_base/tasks`  
> **Secondary:** `scripts`, `poetry`  
> **Complexity:** 🔴 High (3 subtasks)

---

## Objective

During the V6 feature iteration, new platform dependencies were added (`slowapi`, `authlib`, `pyjwt[crypto]`, `sse-starlette`, `argon2-cffi`, `email-validator`, `aiosmtplib`), causing `pyproject.toml` and `poetry.lock` to desynchronize. Simultaneously, Poetry 2.0+ deprecated and extracted the `poetry export` command into `poetry-plugin-export`, causing Docker container builds (`Dockerfile.inference`) to fail with `The command "export" does not exist.`

This base task establishes concrete subtasks to fix the container build configuration, synchronize the Poetry dependency locking system, rebuild all Docker profiles (`core` and `app`), and verify system deployment health via `scripts/deploy.py`.

---

## Acceptance Criteria

### Dependency & Build System Fixes
- [x] `infrastructure/Dockerfile.inference` updated to include `poetry-plugin-export` alongside Poetry 2.0+ installation.
- [x] Host dependency lock file `poetry.lock` synchronized cleanly with `pyproject.toml` without triggering dynamic in-container lock resolutions.
- [x] Docker build process for both `inference` and `gateway` builds cleanly without command errors or timeouts.

### Container & Infrastructure Deployment
- [x] Docker Compose profiles (`core` and `app`) start successfully via `docker compose -f infrastructure/docker-compose.yml --profile core --profile app up -d`.
- [x] Diagnostic health report via `poetry run python scripts/deploy.py --mode all --profile core` passes and verifies PostgreSQL (5432), Qdrant (6333), Redis (6379), Gateway (8000), and Inference (8010).

---

## Linked Subtasks

| ID | Title | File |
|---|---|---|
| S6-13a | Poetry 2.0 Plugin & Lock File Synchronization | [`S6-13a.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-13a.md) |
| S6-13b | Docker Compose Build & Multi-Stage Image Verification | [`S6-13b.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-13b.md) |
| S6-13c | System Deployment & End-to-End Health Diagnostics | [`S6-13c.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-13c.md) |

---

## References

- [`references/deployment/infrastructure.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/deployment/infrastructure.md)
- [`prompts/work_principle.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/prompts/work_principle.md)
- [`prompts/work_verification.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/prompts/work_verification.md)
