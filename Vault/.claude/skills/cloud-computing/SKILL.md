---
name: cloud-computing
description: Cloud infrastructure best practices across AWS, GCP, and Azure — IAM, VPC, cost optimization, serverless, reliability, and security. Use when designing cloud architecture, reviewing IAM policies, or troubleshooting cloud infrastructure.
argument-hint: "[aws|gcp|azure|review] [describe what you're building or paste config/policy]"
model: claude-sonnet-4-6
allowed-tools:
  - Read
  - Edit
  - Write
  - Bash
  - Glob
  - Grep
---

# Cloud Computing Skill

## Modes

`aws` — AWS-specific architecture, IAM, VPC, Lambda, S3, cost guidance
`gcp` — GCP-specific architecture, service accounts, VPC, Cloud Functions, GCS
`azure` — Azure-specific architecture, RBAC, VNet, Functions, Blob Storage
`review` — audit IaC or config for security misconfigurations and cost risks

## Non-Negotiable Rules

- Never hardcode credentials. Use IAM roles/service accounts for application identity.
- Secrets in AWS Secrets Manager / Azure Key Vault / GCP Secret Manager. Rotate every 90 days.
- Block public access on object storage at account/project level — never trust per-bucket settings.
- Least-privilege IAM always. Use Access Analyzer to detect over-permission.
- Enable encryption at rest on all storage (default in 2026, but verify).
- Logs always: CloudTrail, VPC Flow Logs, Cloud Audit Logs — centralized.
- Serverless must be stateless. State goes in DynamoDB, Redis, or equivalent — never function memory.
- Backups live in multiple regions (multi-AZ protects against datacenter failure, not region failure).

## Cost Pattern (Production Default)

40% Reserved/Savings Plans (steady state) + 30% On-Demand (variable) + 30% Spot (batch/fault-tolerant)
= 35–50% blended savings over pure on-demand.

## Lambda Cold Start Mitigation (2026)

- Use SnapStart (Java) or provisioned concurrency for latency-critical paths
- ARM64 Graviton2: 13–24% faster cold start than x86
- Node.js 20 / Python 3.12 runtime — 15–20% faster than older versions
- Minimize package size; avoid container images for functions < 50ms init target

## Security Gate

Every WRITE or review response must end with:
```
SECURITY CHECK: [public storage risk] | [IAM scope] | [credential hardcoding] | CLEAR
```

## Output Format

`aws/gcp/azure` → architecture recommendation, config snippet, cost estimate, security flags
`review` → checklist from REFERENCE.md, PASS/FAIL per item, prioritised fixes
