---
name: edge-computing
description: Edge computing skill covering Cloudflare Workers (+ Durable Objects, KV, R2, Workers AI), Deno Deploy, Vercel Edge Functions, and CloudFront Functions â€” platform selection, edge-safe code patterns, KV/storage, and AI inference at edge. Use when deploying logic close to users, building edge middleware, or running AI inference at edge. Owns edge runtimes; regional serverless functions (Lambda, Cloud Run) â†’ /serverless (not yet built).
argument-hint: "[mode: guide|build|deploy|review] [platform: cloudflare|deno|vercel|cloudfront] [description]"
---

## Scope

/edge-computing owns: edge runtime code, Cloudflare Workers + Durable Objects + KV + R2, Deno Deploy functions, Vercel Edge Middleware, CloudFront Functions, Workers AI inference. General serverless (Lambda, Cloud Run, regional functions) â†’ `/serverless` (not yet built â€” flag gap). Infra deployment â†’ `/infrastructure-as-code`. CI/CD pipeline â†’ `/cicd-pipeline`.

âš  Lambda@Edge is effectively deprecated â€” costs 6x more, 10â€“50x slower than alternatives. Use CloudFront Functions for AWS-native edge.
âš  No Node.js APIs at edge. No `fs`, no `path`, no `require`, no TCP sockets. Web Crypto API (`crypto.subtle.*`) works. Test every npm package for edge compatibility before using.

## Platform Selection

| Signal | Platform |
|---|---|
| General edge, best global coverage (300+ POPs), KV/DO/R2/AI | Cloudflare Workers â€” default |
| GitHub-native workflow, Deno ecosystem, web standards | Deno Deploy |
| Next.js app, Vercel already in use | Vercel Edge Functions / Middleware |
| AWS-native, simple request/response transforms | CloudFront Functions |
| AWS-native, complex logic, 50MB payload, network access needed | Lambda@Edge (legacy â€” prefer CloudFront Functions) |

Cold starts: Cloudflare <1ms Â· Deno <5ms Â· Vercel ~5ms Â· Lambda@Edge 250â€“1200ms

## What Works at Edge

| API | Status |
|---|---|
| `fetch()`, `Request`, `Response`, `Headers` | âœ… All platforms |
| `crypto.subtle.*` (Web Crypto API) | âœ… All platforms |
| `TextEncoder` / `TextDecoder` | âœ… All platforms |
| `ReadableStream` / `TransformStream` | âœ… All platforms |
| `fs`, `path`, `os`, `net`, `child_process` | âŒ None |
| `require()` / CommonJS modules | âŒ None (ESM only) |
| `Buffer` (Node.js) | âŒ â€” use `Uint8Array` |
| Arbitrary npm packages | âš  Test individually |

## Modes

**GUIDE** â€” Platform unspecified. Ask two questions:
1. "AWS-native or platform-agnostic?"
2. "Stateless request transform, or stateful (sessions, counters, rate limiting)?"

**BUILD** â€” Platform named. Generate: Worker/function code + wrangler config (Cloudflare) or config file. Edge-safe patterns only. Full patterns â†’ REFERENCE.md.

**DEPLOY** â€” Wrangler CLI, Deno Deploy CLI, or Vercel CLI setup. Secrets, environment variables, KV bindings, D1/SQLite config. Full setup â†’ REFERENCE.md.

**REVIEW** â€” Audit existing edge code. Full checklist â†’ REFERENCE.md. Report: PASS / WARN / FAIL.

## Security Gate

Every BUILD response ends with this line â€” never skip:
`Security pass: âœ“ no Node.js APIs used âœ“ secrets via environment variables not hardcoded âœ“ CORS headers explicitly set âœ“ input validated before processing âœ“ rate limiting implemented (KV counter or DO) âœ“ no sensitive data in KV keys`

Flag any item that cannot be confirmed. Stop and fix.

## Cross-Skill Integration

- `/node` â€” origin server; edge Worker proxies or augments it
- `/nosql` â€” Cloudflare KV for cache/config; D1 for SQLite at edge
- `/auth` â€” JWT verification at edge (before origin); propose for auth middleware builds
- `/cloud-computing` â€” CloudFront + Lambda@Edge belong there; /edge owns Workers/Deno/Vercel
- `/cicd-pipeline` â€” Wrangler deploy in CI; propose after BUILD
- `/backend` â€” routes here when edge is the task layer

## Anti-patterns

âœ— Never use Lambda@Edge for new AWS edge work â€” use CloudFront Functions
âœ— Never import Node.js built-ins at edge â€” silent failure or runtime error
âœ— Never use CommonJS (`require`) â€” ESM only at edge
âœ— Never hardcode secrets â€” always environment variables / wrangler secrets
âœ— Never use Vercel Edge for non-Next.js projects â€” Cloudflare Workers is better
âœ— Never skip edge compatibility testing for npm packages â€” many fail silently
âœ— Never use Durable Objects without understanding SQLite billing (GA Jan 2026)
âœ— Never assume WinterTC means cross-platform compatibility yet â€” still aspirational


## QA
Before closing this skill session:
- [ ] Mode/language/framework confirmed with operator if ambiguous
- [ ] Output matches stated intent (code runs, no placeholders)
- [ ] Security surface reviewed (injection, auth, input validation)
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.