# Microservices Skill — Reference
Linkerd 2.15 / OpenTelemetry / Kafka + NATS / Pact | Last verified: 2026-04-30

## Research Sources
- Microservices best practices 2026: https://www.theprotec.com/blog/2026/microservices-architecture-advanced-insights-best-practices/
- Linkerd vs Istio 2026: https://devopsboys.com/blog/linkerd-vs-istio-service-mesh-comparison-2026
- OpenTelemetry 2026 guide: https://calmops.com/devops/opentelemetry-observability-2026-complete-guide/
- Message broker comparison 2026: https://www.index.dev/skill-vs-skill/backend-kafka-vs-rabbitmq-vs-nats
- Pact contract testing: https://1xapi.com/blog/api-contract-testing-pact-nodejs-2026

---

## Communication Pattern Decision Table

| Use case | Protocol | Why |
|----------|----------|-----|
| Public API, browser client | REST | Widely compatible, cacheable |
| Internal hot path, low latency | gRPC | 7–10× faster, binary Protobuf, HTTP/2 |
| Decoupled async workflows | Kafka | Replay, event sourcing, 1M+ msg/sec |
| Simple pub-sub, inter-service RPC | NATS | Sub-ms latency, 5–10 MB footprint |
| Complex routing, priority queues | RabbitMQ | Topic exchanges, 10K–100K msg/sec |

---

## Message Broker Selection

```
Need audit trail / replay / analytics? → Kafka
Need sub-millisecond latency / lightweight pub-sub? → NATS
Need complex routing / priority queues / <100K msg/sec? → RabbitMQ

Common production pattern: NATS (service RPC) + Kafka (event log)
```

---

## Circuit Breaker Pattern

```
CLOSED → normal operation
   ↓ threshold of failures exceeded
OPEN → fast-fail, no requests reach downstream
   ↓ after cool-down window
HALF-OPEN → probe 1 request
   ↓ success → CLOSED | failure → OPEN again
```

Implementation libraries: Resilience4j (Java), Polly (.NET), cockatiel (Node.js), circuit-breaker-go (Go)

---

## Retry: Exponential Backoff + Jitter

```python
import random, time

def retry_with_backoff(fn, max_attempts=5, base_delay=1.0, max_delay=32.0):
    for attempt in range(max_attempts):
        try:
            return fn()
        except TransientError as e:
            if attempt == max_attempts - 1:
                raise
            delay = min(base_delay * (2 ** attempt), max_delay)
            jitter = random.uniform(0, delay * 0.1)  # 10% jitter
            time.sleep(delay + jitter)
```

Never use fixed-delay retry. Jitter prevents thundering herd on simultaneous failures.

---

## Saga Pattern (Choreography)

```
Order Service        Payment Service      Inventory Service
     │                     │                     │
     ├─── OrderPlaced ────→│                     │
     │                     ├─── PaymentProcessed ┤
     │                     │             ────────┤─ InventoryReserved
     │                     │                     │
     │   ← compensate ─────┤ (if any step fails) │
```

Choreography: each service subscribes to events and emits its own.
Orchestration: a central saga orchestrator drives the workflow (simpler to reason about).

---

## OpenTelemetry — Mandatory Instrumentation

```javascript
// Node.js — instrument at process entry point
import { NodeSDK } from '@opentelemetry/sdk-node';
import { OTLPTraceExporter } from '@opentelemetry/exporter-trace-otlp-http';
import { Resource } from '@opentelemetry/resources';

const sdk = new NodeSDK({
  resource: new Resource({ 'service.name': 'order-service' }),
  traceExporter: new OTLPTraceExporter({
    url: process.env.OTEL_EXPORTER_OTLP_ENDPOINT,  // OTel Collector
  }),
});

sdk.start();
```

All three signals (traces, metrics, logs) via OTLP to OTel Collector. Never vendor SDK directly.

---

## Service Mesh: Linkerd vs Istio

| | Linkerd 2.15+ | Istio |
|--|---------------|-------|
| Sidecar memory | 5–15 MB | 50–100 MB |
| Added latency | < 1 ms | 1–3 ms |
| mTLS | Automatic, zero-config | Configurable |
| Multi-cluster | No | Yes |
| WASM extensions | No | Yes |
| **Choose when** | Most teams | Multi-cluster federation needed |

---

## Contract Testing (Pact)

```javascript
// Consumer side: define expected contract
const interaction = {
  state: 'user 1 exists',
  uponReceiving: 'GET /users/1',
  withRequest: { method: 'GET', path: '/users/1' },
  willRespondWith: {
    status: 200,
    body: { id: 1, email: like('user@example.com') }
  }
};
```

Consumer publishes pact → provider verifies in CI → blocks merge if contract broken.
Result: 50% reduction in integration test runtime, 40% fewer flaky CI failures.

---

## Anti-Patterns Reference

| Anti-pattern | Symptom | Fix |
|-------------|---------|-----|
| Distributed monolith | Services always deployed together, shared DB | Separate DB per service, async events |
| Chatty services | 10+ synchronous hops per request | BFF aggregation, async messaging |
| Missing idempotency | Duplicate processing on retry | Idempotency key header (UUID), dedupe table |
| No circuit breaker | Cascade failure on downstream outage | Bulkhead + circuit breaker per dependency |
| Synchronous chain > 2 hops | Latency compounds, failure amplifies | Async event-driven for chain > 2 |

---

## REVIEW Checklist

| # | Check | What to look for | Report |
|---|-------|-----------------|--------|
| 1 | No shared DB | Multiple services querying same DB | PASS/FAIL |
| 2 | OTel instrumented | Service missing trace/metric/log export | PASS/FAIL |
| 3 | mTLS configured | Service-to-service calls without mTLS | PASS/FAIL |
| 4 | Circuit breaker | External calls without circuit breaker | PASS/FAIL |
| 5 | Idempotent handlers | POST/message handlers without idempotency key check | PASS/FAIL |
| 6 | Retry has jitter | Fixed-delay retry loops | PASS/FAIL |
| 7 | Async for chains > 2 | Synchronous chains longer than 2 services | PASS/WARN |
| 8 | DDD boundaries | Services split by tech layer, not domain | PASS/WARN |
| 9 | Contract tests | Services without Pact or equivalent contract tests | PASS/WARN |
| 10 | Health endpoints | Service missing /health/live and /health/ready | PASS/FAIL |
