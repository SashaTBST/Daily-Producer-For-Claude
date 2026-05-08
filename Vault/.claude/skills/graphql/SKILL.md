---
name: graphql
description: Security-first GraphQL skill covering schema design (SDL-first and Pothos code-first), server setup (Apollo Server v5, GraphQL Yoga v5, Mercurius), subscriptions (graphql-ws), federation (Apollo Router, Cosmo Router), and client codegen (graphql-codegen, Apollo Client, urql). Use when designing or implementing a GraphQL API, adding subscriptions, federating schemas, or generating TypeScript types from operations. Offers tRPC as an alternative for internal TypeScript-only stacks before proceeding.
argument-hint: "[mode: guide|schema|build|subscriptions|federation|review] [approach: sdl|pothos] [server: apollo|yoga|mercurius] [description]"
---

## Scope

/graphql owns: GraphQL schema design (SDL-first and code-first/Pothos), resolver implementation, subscriptions, federation, client integration, codegen. Does NOT defer to `/api-design` for GraphQL design â€” fully independent.

For internal TypeScript-only stacks with no external API consumers: offer tRPC as a simpler alternative before building GraphQL. Route/middleware wiring â†’ `/node`. Auth layer â†’ `/auth`. Subscription transport â†’ `/websockets-realtime` for raw WebSocket details. Data layer â†’ `/sql` or `/nosql`.

âš  Apollo Server v4 reached EOL January 2026. Use Apollo Server v5 or GraphQL Yoga v5.

## Schema Approach

| Signal | Approach |
|---|---|
| TypeScript team, type safety first, no codegen overhead | Pothos (code-first) â€” default for TS teams |
| Schema as contract, multi-team, design before implementation | SDL-first |
| Public API with external consumers | SDL-first (schema = published contract) |
| Internal API, single team | Pothos (code-first) |

## Server Selection

| Signal | Server |
|---|---|
| General purpose, plugin ecosystem, enterprise | Apollo Server v5 |
| Performance-critical, latency-sensitive, sub-10ms | GraphQL Yoga v5 |
| Fastify application | Mercurius |
| Federated router â€” open-source | Cosmo Router (Apache 2.0, Federation v1/v2) |
| Federated router â€” enterprise/Apollo GraphOS | Apollo Router (Rust) |

## Modes

**GUIDE** â€” Unspecified. Two questions (stop at first decisive answer):
1. "Internal TypeScript monorepo or public/external API?" â€” if internal: offer tRPC first.
2. "SDL-first (schema as contract) or code-first (TypeScript drives schema)?"

**SCHEMA** â€” Design the schema. SDL or Pothos. Covers: types, queries, mutations, input validation, pagination (Relay cursor / offset), error handling, nullable strategy. Full patterns â†’ REFERENCE.md.

**BUILD** â€” Server named. Generate: schema + resolvers + server config + DataLoader for N+1. All files one response. End: propose `/auth` for auth layer and `/tdd` for resolver coverage.

**SUBSCRIPTIONS** â€” Add subscriptions via graphql-ws. Server + client wiring. Migration path from subscriptions-transport-ws. Full patterns â†’ REFERENCE.md.

**FEDERATION** â€” Design federated schema. Default: Cosmo Router (open-source). Apollo Router for existing GraphOS users. Warn about Apollo GraphOS pricing at scale. Full patterns â†’ REFERENCE.md.

**REVIEW** â€” Audit existing GraphQL API. Full checklist â†’ REFERENCE.md. Report each: PASS / WARN / FAIL.

## Security Gate

Every BUILD response ends with this line â€” never skip:
`Security pass: âœ“ safelisting (not APQ) for production âœ“ query depth limit configured âœ“ query complexity limit configured âœ“ introspection disabled in production âœ“ auth verified in resolver context not schema only âœ“ DataLoader on all N+1 relations`

Flag any item that cannot be confirmed. Stop and fix.

## Cross-Skill Integration

- `/node` â€” wire GraphQL server into Express/Fastify/Hono
- `/auth` â€” auth middleware + resolver context; propose before or alongside BUILD
- `/websockets-realtime` â€” raw WebSocket transport for subscriptions; /graphql owns graphql-ws layer above it
- `/api-design` â€” if REST + GraphQL coexist: /api-design owns REST contract, /graphql owns GraphQL
- `/sql` or `/nosql` â€” resolver data layer; propose after SCHEMA
- `/tdd` â€” propose after BUILD: resolver logic, auth gates, N+1 coverage, subscription lifecycle

## Anti-patterns

âœ— Never use Apollo Server v4 â€” EOL January 2026; use v5 or Yoga v5
âœ— Never enable APQ in production without safelisting â€” arbitrary operation DoS vector
âœ— Never enable introspection in production â€” exposes full schema to attackers
âœ— Never resolve relations without DataLoader â€” unbounded N+1 DB calls under load
âœ— Never put auth in schema directives only â€” check in resolver context + middleware
âœ— Never use subscriptions-transport-ws â€” unmaintained; always graphql-ws
âœ— Never default to Apollo Router without evaluating Cosmo â€” Apollo GraphOS pricing risk at scale
âœ— Never skip depth and complexity limits on public APIs â€” query explosion attack surface


## QA
Before closing this skill session:
- [ ] Mode/language/framework confirmed with operator if ambiguous
- [ ] Output matches stated intent (code runs, no placeholders)
- [ ] Security surface reviewed (injection, auth, input validation)
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.