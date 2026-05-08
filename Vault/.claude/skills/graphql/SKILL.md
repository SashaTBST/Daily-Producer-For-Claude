---
name: graphql
description: Security-first GraphQL skill covering schema design (SDL-first and Pothos code-first), server setup (Apollo Server v5, GraphQL Yoga v5, Mercurius), subscriptions (graphql-ws), federation (Apollo Router, Cosmo Router), and client codegen (graphql-codegen, Apollo Client, urql). Use when designing or implementing a GraphQL API, adding subscriptions, federating schemas, or generating TypeScript types from operations. Offers tRPC as an alternative for internal TypeScript-only stacks before proceeding.
argument-hint: "[mode: guide|schema|build|subscriptions|federation|review] [approach: sdl|pothos] [server: apollo|yoga|mercurius] [description]"
---

## Scope

/graphql owns: GraphQL schema design (SDL-first and code-first/Pothos), resolver implementation, subscriptions, federation, client integration, codegen. Does NOT defer to `/api-design` for GraphQL design — fully independent.

For internal TypeScript-only stacks with no external API consumers: offer tRPC as a simpler alternative before building GraphQL. Route/middleware wiring → `/node`. Auth layer → `/auth`. Subscription transport → `/websockets-realtime` for raw WebSocket details. Data layer → `/sql` or `/nosql`.

⚠ Apollo Server v4 reached EOL January 2026. Use Apollo Server v5 or GraphQL Yoga v5.

## Schema Approach

| Signal | Approach |
|---|---|
| TypeScript team, type safety first, no codegen overhead | Pothos (code-first) — default for TS teams |
| Schema as contract, multi-team, design before implementation | SDL-first |
| Public API with external consumers | SDL-first (schema = published contract) |
| Internal API, single team | Pothos (code-first) |

## Server Selection

| Signal | Server |
|---|---|
| General purpose, plugin ecosystem, enterprise | Apollo Server v5 |
| Performance-critical, latency-sensitive, sub-10ms | GraphQL Yoga v5 |
| Fastify application | Mercurius |
| Federated router — open-source | Cosmo Router (Apache 2.0, Federation v1/v2) |
| Federated router — enterprise/Apollo GraphOS | Apollo Router (Rust) |

## Modes

**GUIDE** — Unspecified. Two questions (stop at first decisive answer):
1. "Internal TypeScript monorepo or public/external API?" — if internal: offer tRPC first.
2. "SDL-first (schema as contract) or code-first (TypeScript drives schema)?"

**SCHEMA** — Design the schema. SDL or Pothos. Covers: types, queries, mutations, input validation, pagination (Relay cursor / offset), error handling, nullable strategy. Full patterns → REFERENCE.md.

**BUILD** — Server named. Generate: schema + resolvers + server config + DataLoader for N+1. All files one response. End: propose `/auth` for auth layer and `/tdd` for resolver coverage.

**SUBSCRIPTIONS** — Add subscriptions via graphql-ws. Server + client wiring. Migration path from subscriptions-transport-ws. Full patterns → REFERENCE.md.

**FEDERATION** — Design federated schema. Default: Cosmo Router (open-source). Apollo Router for existing GraphOS users. Warn about Apollo GraphOS pricing at scale. Full patterns → REFERENCE.md.

**REVIEW** — Audit existing GraphQL API. Full checklist → REFERENCE.md. Report each: PASS / WARN / FAIL.

## Security Gate

Every BUILD response ends with this line — never skip:
`Security pass: ✓ safelisting (not APQ) for production ✓ query depth limit configured ✓ query complexity limit configured ✓ introspection disabled in production ✓ auth verified in resolver context not schema only ✓ DataLoader on all N+1 relations`

Flag any item that cannot be confirmed. Stop and fix.

## Cross-Skill Integration

- `/node` — wire GraphQL server into Express/Fastify/Hono
- `/auth` — auth middleware + resolver context; propose before or alongside BUILD
- `/websockets-realtime` — raw WebSocket transport for subscriptions; /graphql owns graphql-ws layer above it
- `/api-design` — if REST + GraphQL coexist: /api-design owns REST contract, /graphql owns GraphQL
- `/sql` or `/nosql` — resolver data layer; propose after SCHEMA
- `/tdd` — propose after BUILD: resolver logic, auth gates, N+1 coverage, subscription lifecycle

## Anti-patterns

✗ Never use Apollo Server v4 — EOL January 2026; use v5 or Yoga v5
✗ Never enable APQ in production without safelisting — arbitrary operation DoS vector
✗ Never enable introspection in production — exposes full schema to attackers
✗ Never resolve relations without DataLoader — unbounded N+1 DB calls under load
✗ Never put auth in schema directives only — check in resolver context + middleware
✗ Never use subscriptions-transport-ws — unmaintained; always graphql-ws
✗ Never default to Apollo Router without evaluating Cosmo — Apollo GraphOS pricing risk at scale
✗ Never skip depth and complexity limits on public APIs — query explosion attack surface
