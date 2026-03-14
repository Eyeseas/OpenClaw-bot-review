# Multi-Arch Docker Publish Implementation Plan

> **For agentic workers:** REQUIRED: Use superpowers:subagent-driven-development (if subagents available) or superpowers:executing-plans to implement this plan. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Extend the existing GitHub Actions Docker publish workflow so the same GHCR tags publish a multi-architecture image manifest for both `linux/amd64` and `linux/arm64`.

**Architecture:** Keep the current single workflow and current `Dockerfile`. Add QEMU emulation support in GitHub Actions, then configure Buildx to build and push both target platforms under the same tags and manifest list. Preserve the existing branch/tag trigger rules and tag generation behavior.

**Tech Stack:** GitHub Actions, Docker Buildx, Docker QEMU, GHCR

---

## Chunk 1: Multi-Arch Workflow Update

### Task 1: Enable multi-architecture image publishing

**Files:**
- Create: `docs/superpowers/plans/2026-03-14-multi-arch-docker-publish.md`
- Modify: `.github/workflows/docker-publish.yml`
- Test: workflow syntax and local repository verification commands

- [ ] **Step 1: Update the workflow to initialize QEMU before Buildx**

Add a `docker/setup-qemu-action@v3` step so the GitHub-hosted runner can emulate `arm64` during image builds.

- [ ] **Step 2: Update the Docker build step to publish both target architectures**

Set `platforms: linux/amd64,linux/arm64` on the existing `docker/build-push-action@v6` step and keep the current tag and push behavior unchanged.

- [ ] **Step 3: Re-read the workflow and verify it still matches the approved design**

Confirm:
- `push` to `main` still pushes `latest`
- `push` `v*` tags still push version tags
- `pull_request` to `main` still builds without pushing
- the same tags now resolve to a multi-arch manifest instead of a single-arch image

- [ ] **Step 4: Run repository verification commands**

Run:
- `git diff --check`
- `sed -n '1,220p' .github/workflows/docker-publish.yml`
- `git status --short`

Expected:
- no whitespace or patch-format errors
- workflow contains QEMU setup and `platforms: linux/amd64,linux/arm64`
- only intended files are modified
