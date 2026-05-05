---
name: edge-computing
description: Edge computing skill covering Cloudflare Workers (+ Durable Objects, KV, R2, Workers AI), Deno Deploy, Vercel Edge Functions, and CloudFront Functions — platform selection, edge-safe code patterns, KV/storage, and AI inference at edge. Use when deploying logic close to users, building edge middleware, or running AI inference at edge. Owns edge runtimes; regional serverless functions (Lambda, Cloud Run) → /serverless (not yet built).
argument-hint: "[mode: guide|build|deploy|review] [platform: cloudflare|deno|vercel|cloudfront] [description]"
---

## Scope

/edge-computing owns: edge runtime code, Cloudflare Workers + Durable Objects + KV + R2, Deno Deploy functions, Vercel Edge Middleware, CloudFront Functions, Workers AI inference. General serverless (Lambda, Cloud Run, regional functions) → `/serverless` (not yet built — flag gap). Infra deployment → `/infrastructure-as-code`. CI/CD pipeline → `/cicd-pipeline`.

⚠ Lambda@Edge is effectively deprecated — costs 6x more, 10–50x slower than alternatives. Use CloudFront Functions for AWS-native edge.
⚠ No Node.js APIs at edge. No `fs`, no `path`, no `require`, no TCP sockets. Web Crypto API (`crypto.subtle.*`) works. Test every npm package for edge compatibility before using.

## Platform Selection

| Signal | Platform |
|---|---|
| General edge, best global coverage (300+ POPs), KV/DO/R2/AI | Cloudflare Workers — default |
| GitHub-native workflow, Deno ecosystem, web standards | Deno Deploy |
| Next.js app, Vercel already in use | Vercel Edge Functions / Middleware |
| AWS-native, simple request/response transforms | CloudFront Functions |
| AWS-native, complex logic, 50MB payload, network access needed | Lambda@Edge (legacy — prefer CloudFront Functions) |

Cold starts: Cloudflare <1ms · Deno <5ms · Vercel ~5ms · Lambda@Edge 250–1200ms

## What Works at Edge

| API | Status |
|---|---|
| `fetch()`, `Request`, `Response`, `Headers` | ✅ All platforms |
| `crypto.subtle.*` (Web Crypto API) | ✅ All platforms |
| `TextEncoder` / `TextDecoder` | ✅ All platforms |
| `ReadableStream` / `TransformStream` | ✅ All platforms |
| `fs`, `path`, `os`, `net`, `child_process` | ❌ None |
| `require()` / CommonJS modules | ❌ None (ESM only) |
| `Buffer` (Node.js) | ❌ — use `Uint8Array` |
| Arbitrary npm packages | ⚠ Test individually |

## Modes

**GUIDE** — Platform unspecified. Ask two questions:
1. "AWS-native or platform-agnostic?"
2. "Stateless request transform, or stateful (sessions, counters, rate limiting)?"

**BUILD** — Platform named. Generate: Worker/function code + wrangler config (Cloudflare) or config file. Edge-safe patterns only. Full patterns → REFERENCE.md.

**DEPLOY** — Wrangler CLI, Deno Deploy CLI, or Vercel CLI setup. Secrets, environment variables, KV bindings, D1/SQLite config. Full setup → REFERENCE.md.

**REVIEW** — Audit existing edge code. Full checklist → REFERENCE.md. Report: PASS / WARN / FAIL.

## Security Gate

Every BUILD response ends with this line — never skip:
`Security pass: ✓ no Node.js APIs used ✓ secrets via environment variables not hardcoded ✓ CORS headers explicitly set ✓ input validated before processing ✓ rate limiting implemented (KV counter or DO) ✓ no sensitive data in KV keys`

Flag any item that cannot be confirmed. Stop and fix.

## Cross-Skill Integration

- `/node` — origin server; edge Worker proxies or augments it
- `/nosql` — Cloudflare KV for cache/config; D1 for SQLite at edge
- `/auth` — JWT verification at edge (before origin); propose for auth middleware builds
- `/cloud-computing` — CloudFront + Lambda@Edge belong there; /edge owns Workers/Deno/Vercel
- `/cicd-pipeline` — Wrangler deploy in CI; propose after BUILD
- `/backend` — routes here when edge is the task layer

## Anti-patterns

✗ Never use Lambda@Edge for new AWS edge work — use CloudFront Functions
✗ Never import Node.js built-ins at edge — silent failure or runtime error
✗ Never use CommonJS (`require`) — ESM only at edge
✗ Never hardcode secrets — always environment variables / wrangler secrets
✗ Never use Vercel Edge for non-Next.js projects — Cloudflare Workers is better
✗ Never skip edge compatibility testing for npm packages — many fail silently
✗ Never use Durable Objects without understanding SQLite billing (GA Jan 2026)
✗ Never assume WinterTC means cross-platform compatibility yet — still aspirational


Every response ends with NEXT MOVE.