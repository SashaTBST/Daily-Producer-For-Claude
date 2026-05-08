---
name: serverless
description: Serverless function implementation covering AWS Lambda (SAM), Google Cloud Run, and Azure Functions v4 â€” handler code, event triggers, cold start optimization, and stateful workflow patterns. Use when deploying event-driven functions, scheduled jobs, HTTP triggers, or stateful workflows without managing servers. Regional serverless only; edge functions â†’ /edge-computing.
argument-hint: "[mode: guide|build|deploy|review] [platform: lambda|cloudrun|azure] [description]"
---

## Scope

/serverless owns: Lambda handler code + SAM config, Cloud Run services + functions, Azure Functions v4, cold start optimization, event trigger wiring (SQS/SNS/EventBridge/HTTP), durable/stateful workflows (Step Functions, Lambda Durable Functions, Azure Durable Functions). Edge functions (Cloudflare Workers, Deno Deploy, CloudFront Functions, Lambda@Edge) â†’ `/edge-computing`. Cloud architecture/IAM/cost â†’ `/cloud-computing`. CDK/Terraform IaC â†’ `/infrastructure-as-code`. CI/CD â†’ `/cicd-pipeline`.

âš  Lambda SnapStart = Java/Python/.NET only. Node.js cold starts: use arm64 (Graviton3) â€” 15â€“20% faster at lower cost.
âš  SST (Serverless Stack) in maintenance mode since 2025 â€” team shifted to OpenCode. Use AWS SAM for new Lambda projects.
âš  Serverless Framework v4 requires paid license for organisations with >$2M ARR.

## Platform Selection

| Signal | Platform |
|---|---|
| AWS, general event-driven or HTTP functions | Lambda + AWS SAM |
| AWS, latency-critical (Java or Python) | Lambda + SnapStart |
| GCP, any serverless | Cloud Run â€” default (60-min timeout, concurrent requests, container-based) |
| GCP, simple single-purpose event snippets | Cloud Run Functions (rebranded from Cloud Functions, Aug 2024) |
| Azure | Azure Functions v4 |
| AWS, stateful / long-running workflows | Lambda Durable Functions (simple, 2026 âš  verify GA) or Step Functions (complex) |
| Azure, stateful workflows | Azure Durable Functions |

## Cold Start â€” Node.js Lambda

1. **arm64 (Graviton3)** â€” 15â€“20% faster than x86, lower cost â€” always use
2. **Memory â‰¥ 1024MB** â€” 2.3GHz CPU vs 1.5GHz at 512MB; cold start drops ~40%
3. **Bundle size** â€” ESM + tree-shaking; AWS SDK v3 modular imports; no ts-node in production
4. **Node.js 22** â€” fastest managed runtime; p95 ~375ms optimised
5. **Provisioned concurrency** â€” latency-critical paths only; cost multiplier

Full patterns + Azure cold start guidance â†’ REFERENCE.md.

## Modes

**GUIDE** â€” Platform unspecified. Ask two questions:
1. "AWS, GCP, or Azure?"
2. "Stateless request/event handling, or stateful workflow?"

**BUILD** â€” Platform named. Generate: TypeScript handler + config (SAM template.yaml / Cloud Run service.yaml / host.json). Cold start mitigations applied by default. Full patterns â†’ REFERENCE.md.

**DEPLOY** â€” SAM CLI / gcloud CLI / Azure Functions Core Tools commands. Secrets, env vars, event trigger wiring.

**REVIEW** â€” Audit existing function. Full checklist â†’ REFERENCE.md. Report: PASS / WARN / FAIL.

## Security Gate

Every BUILD response ends with this line â€” never skip:
`Security pass: âœ“ no secrets hardcoded â€” SSM/Secret Manager/Key Vault used âœ“ input validated at function boundary âœ“ IAM least-privilege (no AdministratorAccess) âœ“ function stateless â€” no state in memory âœ“ cold start mitigations applied âœ“ timeout and memory configured explicitly`

Flag any item that cannot be confirmed. Stop and fix.

## Cross-Skill Integration

- `/cloud-computing` â€” architecture, IAM policy design, cost strategy; routes here for function implementation
- `/edge-computing` â€” Lambda@Edge deprecated; Cloudflare Workers, Deno Deploy, CloudFront Functions
- `/infrastructure-as-code` â€” CDK/Terraform when multi-cloud IaC required; SAM stays in /serverless
- `/cicd-pipeline` â€” SAM/gcloud deploy in CI; propose after BUILD
- `/message-queues` â€” SQS/SNS/EventBridge trigger wiring; Lambda as consumer pattern
- `/node` â€” origin server; Lambda can proxy or fan-out to Node.js service

## Anti-patterns

âœ— Never use SnapStart for Node.js â€” unsupported; use arm64 + 1024MB instead
âœ— Never recommend SST for new projects â€” maintenance mode since 2025
âœ— Never store state in function memory â€” use DynamoDB, Redis, or S3
âœ— Never use Lambda@Edge for new work â€” deprecated; route to `/edge-computing`
âœ— Never use ts-node in Lambda â€” 200â€“500ms cold start penalty; transpile to JS before deploy
âœ— Never use AWS SDK v2 â€” v3 modular imports required for tree-shaking and cold start savings
âœ— Never use Serverless Framework v4 without checking licence cost for orgs >$2M ARR
âœ— Never use container images for functions targeting <50ms init â€” managed runtime is faster

## QA
Before closing this skill session:
- [ ] Mode/language/framework confirmed with operator if ambiguous
- [ ] Output matches stated intent (code runs, no placeholders)
- [ ] Security surface reviewed (injection, auth, input validation)
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.