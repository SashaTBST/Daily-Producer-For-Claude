---
name: cicd-pipeline
description: CI/CD pipeline design and GitHub Actions best practices — OIDC auth, container scanning, SBOM, reusable workflows, deployment strategies, and dependency management. Use when writing or reviewing CI/CD workflows, deployment configs, or security scanning setups.
argument-hint: "[github-actions|security|deploy|review] [paste workflow YAML or describe what to build]"
model: claude-sonnet-4-6
allowed-tools:
  - Read
  - Edit
  - Write
  - Bash
  - Glob
  - Grep
---

# CI/CD Pipeline Skill

## Modes

`github-actions` — write workflows: lint, test, build, cache, matrix, reusable workflows
`security` — OIDC auth, container scanning (Trivy), SBOM (CycloneDX), SAST, Dependabot
`deploy` — deployment strategies: blue-green, canary, rolling; environment gates; health checks
`review` — audit workflows for hardcoded secrets, unpinned actions, missing scans

## Tool Standard 2026

- **GitHub Actions** — default for new projects (33% adoption, agentic workflows)
- **GitLab CI** — enterprise with built-in security/compliance enforcement
- **CircleCI** — parallelism-heavy workloads

## Non-Negotiable Rules

- Pin all actions to commit SHA — not tags or branches (`uses: actions/checkout@a1b2c3d`)
- OIDC for cloud auth (AWS, GCP, Azure). No long-lived credentials in GitHub Secrets.
- `permissions:` block on every job — default to `contents: read`.
- Container scan with Trivy on every image build. Block on CRITICAL/HIGH.
- Generate CycloneDX SBOM on every release build.
- Dependabot or Renovate on all repos — patch/minor auto-merge acceptable.
- Ephemeral runners. Never persist build state between unrelated runs.
- Secrets never interpolated into shell commands that echo (use `--password-stdin`).

## Canonical Stage Order

lint → test → build → security-scan → deploy-staging → smoke-test → [gate] → deploy-prod → post-deploy

Run lint, test, security-scan in parallel (independent). Gate prod deploy on all three passing.

## Deployment Strategy Decision

- **Rolling** — default for K8s stateless services; uses K8s native rolling update
- **Blue-Green** — zero-downtime with instant rollback; doubles infra cost temporarily
- **Canary** — gradual traffic shift (5% → 25% → 100%); monitor error rate before each step

## Security Gate

Every WRITE or review response must end with:
```
SECURITY CHECK: [unpinned actions] | [long-lived creds] | [missing container scan] | [secrets in logs] | CLEAR
```

## Output Format

`github-actions` → complete workflow YAML with pinned actions, caching, and permissions
`security` → OIDC setup, Trivy action config, Dependabot YAML
`deploy` → deployment workflow with health checks and rollback strategy
`review` → checklist from REFERENCE.md, PASS/FAIL per item, prioritised fixes

## Anti-patterns
✗ Never pin actions to tags or branches — commit SHA only
✗ Never use long-lived cloud credentials in GitHub Secrets — OIDC always
✗ Never push container images without a Trivy scan
✗ Never interpolate secrets into echoed shell commands — use `--password-stdin`
✗ Never omit `permissions:` block — default to `contents: read`

Every response ends with NEXT MOVE.