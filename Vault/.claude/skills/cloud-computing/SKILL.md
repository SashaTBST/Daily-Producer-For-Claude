---
name: cloud-computing
description: Cloud infrastructure best practices across AWS, GCP, and Azure â€” IAM, VPC, cost optimization, serverless, reliability, and security. Use when designing cloud architecture, reviewing IAM policies, or troubleshooting cloud infrastructure.
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

`aws` â€” AWS-specific architecture, IAM, VPC, Lambda, S3, cost guidance
`gcp` â€” GCP-specific architecture, service accounts, VPC, Cloud Functions, GCS
`azure` â€” Azure-specific architecture, RBAC, VNet, Functions, Blob Storage
`review` â€” audit IaC or config for security misconfigurations and cost risks

## Non-Negotiable Rules

- Never hardcode credentials. Use IAM roles/service accounts for application identity.
- Secrets in AWS Secrets Manager / Azure Key Vault / GCP Secret Manager. Rotate every 90 days.
- Block public access on object storage at account/project level â€” never trust per-bucket settings.
- Least-privilege IAM always. Use Access Analyzer to detect over-permission.
- Enable encryption at rest on all storage (default in 2026, but verify).
- Logs always: CloudTrail, VPC Flow Logs, Cloud Audit Logs â€” centralized.
- Serverless must be stateless. State goes in DynamoDB, Redis, or equivalent â€” never function memory.
- Backups live in multiple regions (multi-AZ protects against datacenter failure, not region failure).

## Cost Pattern (Production Default)

40% Reserved/Savings Plans (steady state) + 30% On-Demand (variable) + 30% Spot (batch/fault-tolerant)
= 35â€“50% blended savings over pure on-demand.

## Lambda Cold Start Mitigation (2026)

For implementation details (SAM config, arm64, ESM bundling, SQS triggers) â†’ `/serverless`.
Architecture-level guidance only:
- SnapStart (Java/Python/.NET) or provisioned concurrency for latency-critical paths
- arm64 (Graviton3): 15â€“20% faster cold start than x86 at lower cost â€” always use for Node.js
- Node.js 22 / Python 3.12 runtime â€” fastest managed runtimes
- Minimise package size; avoid container images for functions targeting <50ms init

## Security Gate

Every WRITE or review response must end with:
```
SECURITY CHECK: [public storage risk] | [IAM scope] | [credential hardcoding] | CLEAR
```

## Output Format

`aws/gcp/azure` â†’ architecture recommendation, config snippet, cost estimate, security flags
`review` â†’ checklist from REFERENCE.md, PASS/FAIL per item, prioritised fixes

## Anti-patterns
âœ— Never hardcode credentials â€” IAM roles/service accounts only
âœ— Never trust per-bucket ACLs for public access block â€” enforce at account/project level
âœ— Never treat multi-AZ as a substitute for multi-region backups
âœ— Never store state in Lambda/function memory â€” use DynamoDB, Redis, or equivalent

## QA
Before closing this skill session:
- [ ] Mode/language/framework confirmed with operator if ambiguous
- [ ] Output matches stated intent (code runs, no placeholders)
- [ ] Security surface reviewed (injection, auth, input validation)
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.