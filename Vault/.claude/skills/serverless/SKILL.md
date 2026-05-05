---
name: serverless
description: Serverless function implementation covering AWS Lambda (SAM), Google Cloud Run, and Azure Functions v4 — handler code, event triggers, cold start optimization, and stateful workflow patterns. Use when deploying event-driven functions, scheduled jobs, HTTP triggers, or stateful workflows without managing servers. Regional serverless only; edge functions → /edge-computing.
argument-hint: "[mode: guide|build|deploy|review] [platform: lambda|cloudrun|azure] [description]"
---

## Scope

/serverless owns: Lambda handler code + SAM config, Cloud Run services + functions, Azure Functions v4, cold start optimization, event trigger wiring (SQS/SNS/EventBridge/HTTP), durable/stateful workflows (Step Functions, Lambda Durable Functions, Azure Durable Functions). Edge functions (Cloudflare Workers, Deno Deploy, CloudFront Functions, Lambda@Edge) → `/edge-computing`. Cloud architecture/IAM/cost → `/cloud-computing`. CDK/Terraform IaC → `/infrastructure-as-code`. CI/CD → `/cicd-pipeline`.

⚠ Lambda SnapStart = Java/Python/.NET only. Node.js cold starts: use arm64 (Graviton3) — 15–20% faster at lower cost.
⚠ SST (Serverless Stack) in maintenance mode since 2025 — team shifted to OpenCode. Use AWS SAM for new Lambda projects.
⚠ Serverless Framework v4 requires paid license for organisations with >$2M ARR.

## Platform Selection

| Signal | Platform |
|---|---|
| AWS, general event-driven or HTTP functions | Lambda + AWS SAM |
| AWS, latency-critical (Java or Python) | Lambda + SnapStart |
| GCP, any serverless | Cloud Run — default (60-min timeout, concurrent requests, container-based) |
| GCP, simple single-purpose event snippets | Cloud Run Functions (rebranded from Cloud Functions, Aug 2024) |
| Azure | Azure Functions v4 |
| AWS, stateful / long-running workflows | Lambda Durable Functions (simple, 2026 ⚠ verify GA) or Step Functions (complex) |
| Azure, stateful workflows | Azure Durable Functions |

## Cold Start — Node.js Lambda

1. **arm64 (Graviton3)** — 15–20% faster than x86, lower cost — always use
2. **Memory ≥ 1024MB** — 2.3GHz CPU vs 1.5GHz at 512MB; cold start drops ~40%
3. **Bundle size** — ESM + tree-shaking; AWS SDK v3 modular imports; no ts-node in production
4. **Node.js 22** — fastest managed runtime; p95 ~375ms optimised
5. **Provisioned concurrency** — latency-critical paths only; cost multiplier

Full patterns + Azure cold start guidance → REFERENCE.md.

## Modes

**GUIDE** — Platform unspecified. Ask two questions:
1. "AWS, GCP, or Azure?"
2. "Stateless request/event handling, or stateful workflow?"

**BUILD** — Platform named. Generate: TypeScript handler + config (SAM template.yaml / Cloud Run service.yaml / host.json). Cold start mitigations applied by default. Full patterns → REFERENCE.md.

**DEPLOY** — SAM CLI / gcloud CLI / Azure Functions Core Tools commands. Secrets, env vars, event trigger wiring.

**REVIEW** — Audit existing function. Full checklist → REFERENCE.md. Report: PASS / WARN / FAIL.

## Security Gate

Every BUILD response ends with this line — never skip:
`Security pass: ✓ no secrets hardcoded — SSM/Secret Manager/Key Vault used ✓ input validated at function boundary ✓ IAM least-privilege (no AdministratorAccess) ✓ function stateless — no state in memory ✓ cold start mitigations applied ✓ timeout and memory configured explicitly`

Flag any item that cannot be confirmed. Stop and fix.

## Cross-Skill Integration

- `/cloud-computing` — architecture, IAM policy design, cost strategy; routes here for function implementation
- `/edge-computing` — Lambda@Edge deprecated; Cloudflare Workers, Deno Deploy, CloudFront Functions
- `/infrastructure-as-code` — CDK/Terraform when multi-cloud IaC required; SAM stays in /serverless
- `/cicd-pipeline` — SAM/gcloud deploy in CI; propose after BUILD
- `/message-queues` — SQS/SNS/EventBridge trigger wiring; Lambda as consumer pattern
- `/node` — origin server; Lambda can proxy or fan-out to Node.js service

## Anti-patterns

✗ Never use SnapStart for Node.js — unsupported; use arm64 + 1024MB instead
✗ Never recommend SST for new projects — maintenance mode since 2025
✗ Never store state in function memory — use DynamoDB, Redis, or S3
✗ Never use Lambda@Edge for new work — deprecated; route to `/edge-computing`
✗ Never use ts-node in Lambda — 200–500ms cold start penalty; transpile to JS before deploy
✗ Never use AWS SDK v2 — v3 modular imports required for tree-shaking and cold start savings
✗ Never use Serverless Framework v4 without checking licence cost for orgs >$2M ARR
✗ Never use container images for functions targeting <50ms init — managed runtime is faster

Every response ends with NEXT MOVE.