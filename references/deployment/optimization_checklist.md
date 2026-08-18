---
version: 1.0.0
updated: 2026-08-18
id: deployment_optimization
links:
  - ../../workflows/execution/work_verification.md
  - ../../workflows/ci/setup_ci.md
---

# Deployment Optimization Checklist

> **Purpose**: Deployment files (especially Docker) must be **optimized**, not
> just functional. A common failure is that dependencies are downloaded and
> installed but their caches are never deleted, bloating image size. This
> checklist prevents that.

## 1. Docker Image Optimization

- [ ] **Multi-stage builds**: use a builder stage for compilation/install and a
      slim runtime stage for the final image.
- [ ] **Minimal base images**: prefer slim/alpine/distroless where appropriate.
- [ ] **Cache-busting layer ordering**: copy dependency manifests first, install,
      then copy source — so dependency layers are cached and only re-run when
      manifests change.
- [ ] **Delete package caches after install**:
  - Python: `pip install --no-cache-dir ...` and/or `pip cache purge`.
  - Node: `npm ci --omit=dev` then `npm cache clean --force`.
  - apt: `apt-get clean && rm -rf /var/lib/apt/lists/*`.
- [ ] **`.dockerignore`**: exclude `assets/`, `logs/`, `.git/`, caches, and
      build artifacts from the build context.
- [ ] **Combine RUN layers** to reduce layer count where it helps.
- [ ] **Verify image size**: run `docker images` / `docker image inspect` and
      confirm the image is not bloated.

## 2. Non-Docker Deployment

- [ ] Dependency installs clean up their caches (per the package manager).
- [ ] Build artifacts are pruned after build.
- [ ] Environment variables come from `.env.example` (secrets never committed).

## 3. Verification

- `../../workflows/execution/work_verification.md` includes a deployment-optimization check
  before a deployment task is marked `[x]`.
