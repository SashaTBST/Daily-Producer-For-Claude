---
name: message-queues
description: Message queue implementation skill covering Kafka (Confluent CJSK), RabbitMQ (amqplib), and NATS JetStream — producer/consumer setup, broker Docker Compose, dead letter queues, retry with backoff, idempotency, exactly-once semantics, and competing consumers. Use when implementing async message passing, event streaming, or work queue patterns. Broker architecture and topology decisions → /microservices.
argument-hint: "[mode: guide|build|patterns|review] [broker: kafka|rabbitmq|nats] [description]"
---

## Scope

/message-queues owns: broker setup (Docker Compose), producer/consumer implementation, DLQ patterns, retry/backoff, idempotency keys, exactly-once semantics, competing consumers. Broker architecture (topology, service boundaries, when to use which) → `/microservices`. AWS SQS/SNS → `/cloud-computing`. Route/middleware wiring → `/node`.

⚠ KafkaJS is unmaintained (last update 3+ years ago). Never use it. Use Confluent CJSK (`@confluentinc/kafka-javascript`) or `@platformatic/kafka`.

## Broker Selection

| Signal | Broker |
|---|---|
| High-throughput event streaming, ordered logs, replay | Kafka (or Redpanda — Kafka-compatible drop-in) |
| Work queues, task distribution, RPC, complex routing | RabbitMQ |
| Low-latency (<1ms), IoT, edge, cloud-native service mesh | NATS JetStream |
| Throughput >500K msg/s | Kafka |
| Throughput 50–100K msg/s, rich routing needed | RabbitMQ |
| Sub-millisecond, ephemeral (no persistence needed) | NATS core (no JetStream) |

**Redpanda** = Kafka API-compatible, single binary, no JVM. Drop-in for Kafka clients. Use when Kafka operational complexity is the constraint.

## Library Selection

| Broker | Library | Notes |
|---|---|---|
| Kafka | `@confluentinc/kafka-javascript` (CJSK) | Confluent-maintained, librdkafka-backed |
| Kafka (alt) | `@platformatic/kafka` | High-perf, benchmarks better than KafkaJS |
| RabbitMQ | `amqplib` + `amqp-connection-manager` | amqplib v1.0.3 (March 2026); manager adds failover |
| NATS | `nats` (nats.js) | JetStream API built-in |

## Modes

**GUIDE** — Broker unspecified. Ask two questions:
1. "Throughput target and latency tolerance?" — drives broker selection
2. "Need message replay / event log, or fire-and-forget work queues?"

**BUILD** — Broker named. Generate: Docker Compose + producer + consumer. Include DLQ and retry by default. All files one response. End: propose `/tdd` for consumer coverage.

**PATTERNS** — Named pattern requested. Covers: DLQ, retry with exponential backoff, idempotency keys, Kafka exactly-once (EOS), competing consumers. Full implementations → REFERENCE.md.

**REVIEW** — Audit existing message queue code. Checklist → REFERENCE.md. Report each: PASS / WARN / FAIL.

## Security Gate

Every BUILD response ends with this line — never skip:
`Security pass: ✓ broker auth configured (SASL/TLS or user/pass) ✓ consumers idempotent ✓ DLQ wired for poison messages ✓ max retry limit set ✓ message size limits configured ✓ no secrets in message payloads`

Flag any item that cannot be confirmed. Stop and fix.

## Cross-Skill Integration

- `/microservices` — broker topology, service boundary design, event-driven architecture
- `/node` — wire producer/consumer into application server
- `/nosql` — Redis for idempotency key store, deduplication cache
- `/docker-kubernetes` — broker cluster setup beyond Docker Compose
- `/cloud-computing` — SQS/SNS/EventBridge (AWS-managed message services)
- `/tdd` — propose after BUILD: consumer idempotency, DLQ routing, retry exhaustion paths

## Anti-patterns

✗ Never use KafkaJS — unmaintained 3+ years; use Confluent CJSK or @platformatic/kafka
✗ Never use NATS push consumers for horizontal scaling — use pull consumers (JetStream)
✗ Never implement Kafka EOS without stable transactionalId per partition — see REFERENCE.md
✗ Never skip DLQ — poison messages block consumers indefinitely without it
✗ Never retry without backoff and max attempts — infinite retry loops fill queues
✗ Never store secrets or PII in message payloads — encrypt or reference by ID
✗ Never put SQS/SNS here — they belong in /cloud-computing
✗ Never assume DLQ patterns are portable across brokers — each is broker-specific
