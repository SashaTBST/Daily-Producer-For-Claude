---
name: cicd-pipeline
description: CI/CD pipeline design and GitHub Actions best practices â€” OIDC auth, container scanning, SBOM, reusable workflows, deployment strategies, and dependency management. Use when writing or reviewing CI/CD workflows, deployment configs, or security scanning setups.
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

`github-actions` â€” write workflows: lint, test, build, cache, matrix, reusable workflows
`security` â€” OIDC auth, container scanning (Trivy), SBOM (CycloneDX), SAST, Dependabot
`deploy` â€” deployment strategies: blue-green, canary, rolling; environment gates; health checks
`review` â€” audit workflows for hardcoded secrets, unpinned actions, missing scans

## Tool Standard 2026

- **GitHub Actions** â€” default for new projects (33% adoption, agentic workflows)
- **GitLab CI** â€” enterprise with built-in security/compliance enforcement
- **CircleCI** â€” parallelism-heavy workloads

## Non-Negotiable Rules

- Pin all actions to commit SHA â€” not tags or branches (`uses: actions/checkout@a1b2c3d`)
- OIDC for cloud auth (AWS, GCP, Azure). No long-lived credentials in GitHub Secrets.
- `permissions:` block on every job â€” default to `contents: read`.
- Container scan with Trivy on every image build. Block on CRITICAL/HIGH.
- Generate CycloneDX SBOM on every release build.
- Dependabot or Renovate on all repos â€” patch/minor auto-merge acceptable.
- Ephemeral runners. Never persist build state between unrelated runs.
- Secrets never interpolated into shell commands that echo (use `--password-stdin`).

## Canonical Stage Order

lint â†’ test â†’ build â†’ security-scan â†’ deploy-staging â†’ smoke-test â†’ [gate] â†’ deploy-prod â†’ post-deploy

Run lint, test, security-scan in parallel (independent). Gate prod deploy on all three passing.

## Deployment Strategy Decision

- **Rolling** â€” default for K8s stateless services; uses K8s native rolling update
- **Blue-Green** â€” zero-downtime with instant rollback; doubles infra cost temporarily
- **Canary** â€” gradual traffic shift (5% â†’ 25% â†’ 100%); monitor error rate before each step

## Security Gate

Every WRITE or review response must end with:
```
SECURITY CHECK: [unpinned actions] | [long-lived creds] | [missing container scan] | [secrets in logs] | CLEAR
```

## Output Format

`github-actions` â†’ complete workflow YAML with pinned actions, caching, and permissions
`security` â†’ OIDC setup, Trivy action config, Dependabot YAML
`deploy` â†’ deployment workflow with health checks and rollback strategy
`review` â†’ checklist from REFERENCE.md, PASS/FAIL per item, prioritised fixes

## Anti-patterns
âœ— Never pin actions to tags or branches â€” commit SHA only
âœ— Never use long-lived cloud credentials in GitHub Secrets â€” OIDC always
âœ— Never push container images without a Trivy scan
âœ— Never interpolate secrets into echoed shell commands â€” use `--password-stdin`
âœ— Never omit `permissions:` block â€” default to `contents: read`

## QA
Before closing this skill session:
- [ ] Mode/language/framework confirmed with operator if ambiguous
- [ ] Output matches stated intent (code runs, no placeholders)
- [ ] Security surface reviewed (injection, auth, input validation)
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.