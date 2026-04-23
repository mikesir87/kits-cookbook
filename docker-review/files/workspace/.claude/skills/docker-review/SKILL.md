---
name: docker-review
description: Review a Dockerfile for best practices — layer caching, image size, security, and reproducibility. Use when the user asks to review, audit, or improve a Dockerfile.
---

When reviewing a Dockerfile, check for these categories in order. Flag findings
with severity (critical / warning / suggestion) and a suggested fix.

## 1. Base image

- Is the base image pinned to a specific digest or tag? `latest` is a warning.
- Is the base image appropriate for the workload? Prefer slim/distroless for
  production images when the build doesn't need a full OS.

## 2. Layer order and caching

- Are dependency files (`package.json`, `requirements.txt`, `go.mod`) copied
  and installed **before** application source? If not, every source change
  busts the dependency-install cache. Critical.
- Are `RUN` commands consolidated where it makes sense (e.g. `apt-get update`
  and `apt-get install` in the same layer)?
- Does `apt-get install` clean up `/var/lib/apt/lists/*` in the same layer?

## 3. Image size

- Are build dependencies separated from runtime via multi-stage builds?
- Are there any `COPY . .` steps that would pull in a large working tree
  without a `.dockerignore`?
- Are package managers called with a no-cache flag where applicable
  (`--no-cache-dir` for pip, `--no-install-recommends` for apt)?

## 4. Security

- Does the image run as a non-root user (`USER` directive)? Running as root
  is a warning.
- Are secrets being baked in via `ARG` or `ENV`? Critical if a secret is
  visible in the image history — flag it and suggest BuildKit secrets.
- Is the final image exposing only the ports it needs?

## 5. Reproducibility

- Are package versions pinned in the install steps?
- Is the Dockerfile frontend line present (`# syntax=docker/dockerfile:...`)
  if BuildKit features are used?

## Output format

Respond with:

1. A one-sentence verdict (e.g., "Looks solid with two warnings.").
2. A table of findings: severity · location (line number) · issue · fix.
3. A rewritten Dockerfile only if the user asks for it, or if more than three
   critical findings exist.
