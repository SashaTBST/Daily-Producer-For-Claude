# CI/CD Pipeline Skill — Reference
GitHub Actions / Trivy / CycloneDX SBOM / Dependabot | Last verified: 2026-04-30

## Research Sources
- GitHub Actions security 2026: https://github.blog/news-insights/product-news/whats-coming-to-our-github-actions-2026-security-roadmap/
- CI/CD pipeline best practices 2026: https://medium.com/@krishnafattepurkar/building-a-production-ready-ci-cd-pipeline-the-complete-2026-guide-b3d6a661ecd8
- Container scanning Trivy vs Grype: https://aquilax.ai/blog/scan-docker-images-vulnerabilities
- Deployment strategies 2026: https://dasroot.net/posts/2026/02/mastering-continuous-deployment-strategies-2026/
- OWASP CI/CD Security Risks: https://owasp.org/www-project-top-10-ci-cd-security-risks/

---

## Canonical Pipeline Stage Order

```
checkout → lint → test (unit) → build → security-scan → deploy-staging → smoke-test → [gate] → deploy-prod → post-deploy
```

- lint + test + security-scan run in parallel (no dependencies)
- Gate can be manual approval or automated metric check
- Post-deploy: health check, error rate check, rollback trigger

---

## OIDC Auth — No Long-Lived Credentials (AWS)

```yaml
# ✗ Never store cloud keys in GitHub Secrets
# ✓ OIDC: short-lived per-job token

permissions:
  contents: read
  id-token: write    # REQUIRED for OIDC

jobs:
  deploy:
    steps:
      - uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: arn:aws:iam::123456789012:role/GitHubActionsRole
          aws-region: us-east-1
      # No AWS keys stored anywhere — token expires when job ends
```

GCP equivalent: `google-github-actions/auth` with Workload Identity Federation
Azure equivalent: `azure/login` with federated credentials

---

## Container Scan + SBOM

```yaml
- name: Build image
  uses: docker/build-push-action@v5
  with:
    push: false
    tags: myapp:${{ github.sha }}
    outputs: type=docker,dest=/tmp/image.tar

- name: Trivy vulnerability scan
  uses: aquasecurity/trivy-action@v0.36.0
  with:
    input: /tmp/image.tar
    format: sarif
    output: trivy-results.sarif
    severity: CRITICAL,HIGH      # block on these

- name: Generate CycloneDX SBOM
  uses: aquasecurity/trivy-action@v0.36.0
  with:
    input: /tmp/image.tar
    format: cyclonedx
    output: sbom.cyclonedx.json

- name: Upload to GitHub Security tab
  uses: github/codeql-action/upload-sarif@v3
  with:
    sarif_file: trivy-results.sarif
```

---

## Reusable Workflow Pattern

```yaml
# .github/workflows/deploy.yml (reusable)
on:
  workflow_call:
    inputs:
      environment:
        required: true
        type: string
    secrets:
      registry-password:
        required: true

jobs:
  deploy:
    environment: ${{ inputs.environment }}
    steps:
      - run: echo "Deploying to ${{ inputs.environment }}"

# .github/workflows/main.yml (caller)
jobs:
  deploy-staging:
    uses: ./.github/workflows/deploy.yml
    with:
      environment: staging
    secrets:
      registry-password: ${{ secrets.REGISTRY_PASSWORD }}
```

Secrets are NOT inherited automatically — must be explicitly passed via `secrets:`.

---

## Dependency Caching Patterns

```yaml
# npm — uses built-in cache in setup-node
- uses: actions/setup-node@v4
  with:
    node-version: '20'
    cache: 'npm'          # caches ~/.npm automatically

# Cargo / Rust
- uses: actions/cache@v4
  with:
    path: |
      ~/.cargo/registry
      ~/.cargo/git
      target
    key: ${{ runner.os }}-cargo-${{ hashFiles('**/Cargo.lock') }}

# Go
- uses: actions/setup-go@v5
  with:
    go-version: '1.23'
    cache: true           # built-in cache support
```

---

## Dependabot Configuration

```yaml
# .github/dependabot.yml
version: 2
updates:
  - package-ecosystem: npm
    directory: '/'
    schedule:
      interval: daily
    auto-delete-branch: true

  - package-ecosystem: github-actions
    directory: '/'
    schedule:
      interval: weekly     # pin GitHub Actions SHAs
    commit-message:
      prefix: 'ci: '

  - package-ecosystem: docker
    directory: '/'
    schedule:
      interval: weekly
```

---

## Deployment Strategy Decision

| Strategy | Rollback | Cost | Best for |
|----------|----------|------|---------|
| Rolling | Slow (replace instances) | None | K8s stateless services |
| Blue-Green | Instant (flip DNS) | 2× infra temporarily | Critical services, zero-downtime required |
| Canary | Instant (cut traffic) | Minimal overhead | High-risk changes, A/B testing |

Progressive delivery: canary → observe error rate → promote or rollback.
Never expose all users to unvalidated changes simultaneously.

---

## REVIEW Checklist

| # | Check | What to look for | Report |
|---|-------|-----------------|--------|
| 1 | Actions pinned to SHA | `uses: actions/checkout@main` (mutable tag) | PASS/FAIL |
| 2 | OIDC instead of long-lived creds | Cloud keys stored in GitHub Secrets | PASS/FAIL |
| 3 | `permissions:` block set | No permissions block (defaults too broad) | PASS/FAIL |
| 4 | Container scan in pipeline | No Trivy or Grype step | PASS/FAIL |
| 5 | SBOM generated on release | No SBOM artifact on release build | PASS/WARN |
| 6 | Secrets not echoed | `run: echo ${{ secrets.TOKEN }}` or similar | PASS/FAIL |
| 7 | Dependency manager configured | No Dependabot or Renovate | PASS/WARN |
| 8 | Rollback plan documented | Deploy job with no rollback strategy | PASS/WARN |
| 9 | Ephemeral runners | Self-hosted persistent runners | PASS/WARN |
| 10 | Post-deploy health check | Deploy job ends without smoke test | PASS/WARN |
