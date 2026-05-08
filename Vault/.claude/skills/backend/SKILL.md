---
name: backend
description: Backend development director â€” routes to the right backend skill based on task layer, tool, and context. Use when building APIs, data layers, infrastructure, or architecture for web, browser games, or native games. Invoked by any director or standalone.
argument-hint: "[task description] â€” name the layer or tool if known (e.g. 'PostgreSQL schema', 'Terraform AWS setup', 'REST API design', 'Dockerfile', 'system architecture')"
---

## Role

Backend routing layer. Infers the correct skill from task context. Does not generate backend code or config â€” delegates to the skill that owns that layer.

IP separation applies: no project-specific business logic in routing decisions.

## Invocation

- Standalone: `/backend [task description]`
- From `/web-game` for game server API or WebSocket backend
- From `/game-maker` for game backend infrastructure
- From any director when the task layer = backend

## Routing

If the task names a tool explicitly (Postgres, Terraform, Docker, Fastify, etc.) â†’ skip routing, go direct to that skill.

Otherwise infer from task context using layer priority:
**Architecture â†’ API â†’ Implementation â†’ Data â†’ Infrastructure â†’ Delivery**

| Task | Route |
|---|---|
| System architecture, scalability, SPOFs, capacity estimation | `/system-design` |
| Distributed services, service mesh, inter-service comms | `/microservices` |
| REST API design, OpenAPI spec, endpoint structure | `/api-design` |
| Node.js/Bun server, Fastify/Express routes, middleware | `/node` |
| Relational schema, SQL queries, migrations, indexes | `/sql` |
| MongoDB documents, Valkey/Redis cache, document schema | `/nosql` |
| Dockerfile, Docker Compose, K8s manifests, Helm | `/docker-kubernetes` |
| Cloud architecture, AWS/GCP/Azure services, IAM, VPC | `/cloud-computing` |
| Terraform/OpenTofu HCL, modules, remote state | `/infrastructure-as-code` |
| GitHub Actions, CI/CD pipeline, deployment strategy | `/cicd-pipeline` |
| Auth, OAuth2/OIDC, JWT, passkeys/WebAuthn, session management | `/auth` |

**Data layer ambiguity:** task references a database but type is unspecified â†’ ask ONE question: "Relational data (tables, joins) or document/cache data (flexible schema, key-value)?"

**Zero-signal ambiguity:** task has no layer or tool signal â†’ ask ONE question: "What layer â€” API design, server implementation, database, deployment infrastructure, or architecture?"

Multi-layer tasks: list all relevant skills in layer-priority order. State which layer to start with.

For sql vs nosql, cloud-computing vs infrastructure-as-code, and api-design vs node decision rules â†’ see REFERENCE.md.

## Out of Scope

These backend types have no dedicated skill yet â€” flag the gap and redirect:

| Type | Redirect |
|---|---|
| Auth / OAuth2 / passkeys / JWT / session management | `/auth` â€” built 2026-05-02 |
| GraphQL API (design or implementation) | `/graphql` â€” built 2026-05-02 |
| WebSocket / realtime / SSE / state sync | `/websockets-realtime` â€” built 2026-05-02 |
| Message queue implementation (Kafka, RabbitMQ, NATS) | `/message-queues` â€” built 2026-05-02 |
| Vector databases (pgvector, Pinecone, Weaviate) | `/vector-db` â€” built 2026-05-02 |
| Game backend (Nakama, Colyseus, matchmaking) | `/game-backend` â€” built 2026-05-02 |
| Edge computing (Cloudflare Workers, Deno Deploy) | `/edge-computing` â€” built 2026-05-02 |
| Serverless 2.0 / durable functions | `/serverless` â€” built 2026-05-02 |

When a gap skill is triggered: name it, state it's not built yet, redirect to nearest primitive.

## Pipeline

Director signals route â†’ skill executes â†’ skill proposes next layer â†’ user re-invokes `/backend` for next layer or continues in sub-skill.

End every response with NEXT MOVE proposing the next backend layer.

## Anti-patterns

âœ— Never force `/api-design` before `/node` if task is implementation-specific â€” offer the sequence, don't gate it
âœ— Never route data layer tasks without asking if database type is unspecified
âœ— Never route "set up AWS infrastructure" without checking abstraction level â€” see REFERENCE.md
âœ— Never ask routing questions when a tool or layer is already named in the task
âœ— Never generate backend code or config directly â€” route to the owning skill
âœ— Never route auth, WebSocket, GraphQL, or game-backend tasks â€” flag gap and redirect


## QA
Before closing this skill session:
- [ ] Mode/language/framework confirmed with operator if ambiguous
- [ ] Output matches stated intent (code runs, no placeholders)
- [ ] Security surface reviewed (injection, auth, input validation)
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.