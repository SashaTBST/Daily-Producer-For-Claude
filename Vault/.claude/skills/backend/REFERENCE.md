# backend — Reference

## sql vs nosql Decision

Ask ONE question only when data layer is referenced but type is unspecified.
If a tool is named (Postgres, MongoDB, Valkey, Redis) → route directly, no question.

| Signal | Route |
|---|---|
| Tables, joins, foreign keys, migrations, ACID | `/sql` |
| Flexible/nested documents, variable schema | `/nosql` — MONGO mode |
| Cache, key-value store, rate limiting, pub/sub | `/nosql` — CACHE mode |
| "Need a database for user profiles" (no type given) | Ask: relational or document? |

---

## cloud-computing vs infrastructure-as-code Decision

Decision axis = abstraction level.

| Signal | Route |
|---|---|
| Which cloud services to use, architecture topology | `/cloud-computing` |
| IAM policy design, VPC structure, cost strategy | `/cloud-computing` |
| Writing Terraform / OpenTofu HCL files | `/infrastructure-as-code` |
| Module structure, remote state, security scanning | `/infrastructure-as-code` |
| "Set up AWS" (no config yet) | `/cloud-computing` first — then `/infrastructure-as-code` for config |

---

## system-design vs microservices Decision

| Signal | Route |
|---|---|
| High-level architecture from scratch, capacity estimation | `/system-design` |
| SLO/SLI/SLA, SPOF analysis, CAP theorem | `/system-design` |
| Service boundary design, DDD bounded contexts | `/microservices` |
| Inter-service communication, circuit breakers, mesh | `/microservices` |
| "Design a scalable backend" (no distributed services yet) | `/system-design` |

---

## api-design → node Sequence

api-design comes BEFORE node for new API builds — design the contract, then implement.
Route directly to `/node` if task is implementation-specific (routes, handlers, middleware).

| Signal | Route |
|---|---|
| "Design an API for X" / "What endpoints should I have?" | `/api-design` first |
| "Generate an OpenAPI spec" | `/api-design` — OPENAPI mode |
| "Write a Fastify route for X" / "Add middleware for Y" | `/node` directly |
| "Review my API security" | `/api-design` — SECURITY mode |

After `/api-design` DESIGN mode: always propose `/node` as the next layer.

---

## Multi-Layer Task Stacking

Priority order: **Architecture → API → Implementation → Data → Infrastructure → Delivery**

Route from the highest applicable layer. State which layer to start with.

Example: "Build a REST API with Postgres, containerised, deployed to AWS"
→ `/api-design` → `/node` → `/sql` → `/docker-kubernetes` → `/cloud-computing` → `/infrastructure-as-code` → `/cicd-pipeline`

Surface the full stack in one response. User picks where to start.

---

## Planned Skills Backlog

Flag gap, name planned skill, redirect to nearest primitive when triggered.

| Planned Skill | Scope | Nearest Primitive |
|---|---|---|
| `/auth` | OAuth2/OIDC, JWT, passkeys/WebAuthn, session management | ✅ Built 2026-05-02 |
| `/graphql` | GraphQL API design, resolvers, subscriptions | ✅ Built 2026-05-02 |
| `/websockets-realtime` | WebSocket servers, SSE, realtime state sync | ✅ Built 2026-05-02 |
| `/message-queues` | Kafka/RabbitMQ/NATS implementation | ✅ Built 2026-05-02 |
| `/vector-db` | pgvector, Pinecone, Weaviate (AI backends) | ✅ Built 2026-05-02 |
| `/game-backend` | Nakama, Colyseus, matchmaking, leaderboards | ✅ Built 2026-05-02 |
| `/edge-computing` | Cloudflare Workers, Deno Deploy, edge functions | ✅ Built 2026-05-02 |
| `/serverless` | Lambda, Cloud Run, Azure Functions, durable workflows | ✅ Built 2026-05-02 |

**Future directors:** `/cicd-pipeline` and `/system-design` will eventually become directors with sub-skill routing. Both are in `/backend` scope now — route directly.

**Update obligation:** When any planned skill is built, add it to the routing table in SKILL.md and remove it from this list.

---

## Security Inherited from Sub-Skills

| Sub-skill | Key security gate |
|---|---|
| `/node` | No $queryRawUnsafe; input validated before DB write; no secrets in code; async errors handled |
| `/api-design` | Nouns in URLs; versioned; rate limiting documented; auth on all endpoints |
| `/microservices` | mTLS between services; idempotency keys; no shared databases |
| `/cloud-computing` | No hardcoded credentials; least-privilege IAM; no public object storage |
| `/docker-kubernetes` | No root user; resource limits set; pinned image tags; health probes present |
| `/infrastructure-as-code` | No hardcoded creds in HCL; remote state; state locking; sensitive outputs marked |
| `/cicd-pipeline` | Pinned actions to commit SHA; OIDC auth; container scan; no secrets in logs |
| `/system-design` | No SPOF without mitigation; stateless services; circuit breakers on all external calls |
| `/sql` | Parameterized queries; no SELECT *; least-privilege grants; migration files only |
| `/nosql` | No unsanitized operators; limit() on queries; TTL on all cache keys |

---

## Caller Integration Signals

Routing logic is identical for all callers. What changes is the context sentence.

| Caller | Context signal |
|---|---|
| `/web-game` | Game server API or WebSocket backend for browser game |
| `/game-maker` | Game backend infrastructure |
| Standalone | General web, API, or infrastructure task |
| Any director | Backend layer required for [project context] |

`/backend` does not load any director — routing is one-way (director → sub-skill, not circular).
