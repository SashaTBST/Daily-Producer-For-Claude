---
name: message-queues
description: Message queue implementation skill covering Kafka (Confluent CJSK), RabbitMQ (amqplib), and NATS JetStream â€” producer/consumer setup, broker Docker Compose, dead letter queues, retry with backoff, idempotency, exactly-once semantics, and competing consumers. Use when implementing async message passing, event streaming, or work queue patterns. Broker architecture and topology decisions â†’ /microservices.
argument-hint: "[mode: guide|build|patterns|review] [broker: kafka|rabbitmq|nats] [description]"
---

## Scope

/message-queues owns: broker setup (Docker Compose), producer/consumer implementation, DLQ patterns, retry/backoff, idempotency keys, exactly-once semantics, competing consumers. Broker architecture (topology, service boundaries, when to use which) â†’ `/microservices`. AWS SQS/SNS â†’ `/cloud-computing`. Route/middleware wiring â†’ `/node`.

âš  KafkaJS is unmaintained (last update 3+ years ago). Never use it. Use Confluent CJSK (`@confluentinc/kafka-javascript`) or `@platformatic/kafka`.

## Broker Selection

| Signal | Broker |
|---|---|
| High-throughput event streaming, ordered logs, replay | Kafka (or Redpanda â€” Kafka-compatible drop-in) |
| Work queues, task distribution, RPC, complex routing | RabbitMQ |
| Low-latency (<1ms), IoT, edge, cloud-native service mesh | NATS JetStream |
| Throughput >500K msg/s | Kafka |
| Throughput 50â€“100K msg/s, rich routing needed | RabbitMQ |
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

**GUIDE** â€” Broker unspecified. Ask two questions:
1. "Throughput target and latency tolerance?" â€” drives broker selection
2. "Need message replay / event log, or fire-and-forget work queues?"

**BUILD** â€” Broker named. Generate: Docker Compose + producer + consumer. Include DLQ and retry by default. All files one response. End: propose `/tdd` for consumer coverage.

**PATTERNS** â€” Named pattern requested. Covers: DLQ, retry with exponential backoff, idempotency keys, Kafka exactly-once (EOS), competing consumers. Full implementations â†’ REFERENCE.md.

**REVIEW** â€” Audit existing message queue code. Checklist â†’ REFERENCE.md. Report each: PASS / WARN / FAIL.

## Security Gate

Every BUILD response ends with this line â€” never skip:
`Security pass: âœ“ broker auth configured (SASL/TLS or user/pass) âœ“ consumers idempotent âœ“ DLQ wired for poison messages âœ“ max retry limit set âœ“ message size limits configured âœ“ no secrets in message payloads`

Flag any item that cannot be confirmed. Stop and fix.

## Cross-Skill Integration

- `/microservices` â€” broker topology, service boundary design, event-driven architecture
- `/node` â€” wire producer/consumer into application server
- `/nosql` â€” Redis for idempotency key store, deduplication cache
- `/docker-kubernetes` â€” broker cluster setup beyond Docker Compose
- `/cloud-computing` â€” SQS/SNS/EventBridge (AWS-managed message services)
- `/tdd` â€” propose after BUILD: consumer idempotency, DLQ routing, retry exhaustion paths

## Anti-patterns

âœ— Never use KafkaJS â€” unmaintained 3+ years; use Confluent CJSK or @platformatic/kafka
âœ— Never use NATS push consumers for horizontal scaling â€” use pull consumers (JetStream)
âœ— Never implement Kafka EOS without stable transactionalId per partition â€” see REFERENCE.md
âœ— Never skip DLQ â€” poison messages block consumers indefinitely without it
âœ— Never retry without backoff and max attempts â€” infinite retry loops fill queues
âœ— Never store secrets or PII in message payloads â€” encrypt or reference by ID
âœ— Never put SQS/SNS here â€” they belong in /cloud-computing
âœ— Never assume DLQ patterns are portable across brokers â€” each is broker-specific


## QA
Before closing this skill session:
- [ ] Mode/language/framework confirmed with operator if ambiguous
- [ ] Output matches stated intent (code runs, no placeholders)
- [ ] Security surface reviewed (injection, auth, input validation)
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.