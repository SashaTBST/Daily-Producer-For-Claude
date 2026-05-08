---
name: microservices
description: Microservices architecture patterns, inter-service communication, resilience, and observability. Use when designing, reviewing, or debugging distributed services — service mesh, message brokers, circuit breakers, contract testing.
argument-hint: "[design|review|patterns|security] [describe your system or paste service code]"
model: claude-sonnet-4-6
allowed-tools:
  - Read
  - Edit
  - Write
  - Bash
  - Glob
  - Grep
---

# Microservices Skill

## Modes

`design` — architect a new service or system boundary based on DDD principles
`review` — audit an existing service for anti-patterns, tight coupling, or security gaps
`patterns` — explain or apply a specific pattern (circuit breaker, saga, CQRS, event sourcing)
`security` — mTLS, SPIFFE workload identity, zero-trust networking between services

## Non-Negotiable Rules

- Services own their data. No shared databases between services.
- Design by business domain (DDD bounded contexts), not technical layer.
- Every API and async consumer must be idempotent. Duplicates are inevitable.
- Instrument every service with OpenTelemetry (traces + metrics + logs). No vendor-specific SDKs.
- Use mTLS via service mesh (Linkerd 2.15+ default; Istio for multi-cluster). Never trust the network.
- Circuit breaker on every external service call. Retry with exponential backoff + jitter only.
- Sensitive operations must be idempotent with idempotency keys (UUID in headers).

## Communication Pattern

- **REST** — public APIs, external integrations, browser clients
- **gRPC** — internal hot paths requiring latency SLA (7–10× faster than REST)
- **Async messaging** — event-driven workflows, decoupling, eventual consistency
  - Kafka: event sourcing, replay, audit trail, 1M+ msg/sec
  - NATS: sub-ms latency, simple pub-sub, inter-service RPC
  - RabbitMQ: complex routing, priority queues

## Anti-Pattern Gates

Before approving any design, check:
- [ ] No shared database between services
- [ ] No synchronous call chains deeper than 2 hops (async-first for chains)
- [ ] No fine-grained chatty calls (aggregate at BFF or async)
- [ ] No distributed monolith (services must be deployable independently)

## Security Gate

Every WRITE or design response must end with:
```
SECURITY CHECK: [injection risk] | [auth/mTLS gap] | [idempotency gap] | CLEAR
```

## Output Format

`design` → service boundary diagram (text), communication pattern, data ownership, open questions
`review` → checklist from REFERENCE.md, PASS/FAIL per item, prioritised fixes
`patterns` → explain the pattern, when to use, implementation snippet, tradeoffs
`security` → threat surface, mTLS setup, SPIFFE identity, authorization policies
