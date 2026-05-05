# System Design Skill — Reference
Distributed systems / SLO / Resilience patterns | Last verified: 2026-04-30

## Research Sources
- System design interview guide: https://designgurus.substack.com/p/your-pre-interview-checklist-15-things
- CAP theorem practical: https://www.hellointerview.com/learn/system-design/core-concepts/cap-theorem
- SLO vs SLA vs SLI: https://nurbak.com/en/blog/slo-vs-sla/
- Resilience patterns 2026: https://technori.com/2026/02/24230-the-complete-guide-to-resilience-patterns-in-distributed-systems/gabriel/
- Exponential backoff and jitter: https://aws.amazon.com/blogs/architecture/exponential-backoff-and-jitter/

---

## Back-of-Envelope Estimation — Reference Numbers

| Operation | Latency | Notes |
|-----------|---------|-------|
| L1 cache | ~1 ns | |
| Main memory | ~100 ns | L1 is 14× faster than L2 |
| Datacenter network | ~1 µs | 1,000× slower than memory |
| SSD read | ~100 µs | |
| Cross-region network | ~100 ms | |
| Disk seek (HDD) | ~10 ms | 100,000× slower than memory |
| User-perceptible | > 250 ms | Design target for real-time |

**Parallel vs sequential latency:**
- Sequential: sum all components
- Parallel: max latency of any one component

---

## SLO / SLI / SLA Stack

```
SLI: measured value            "99.7% of requests succeeded last 30 days"
SLO: internal target           "maintain SLI ≥ 99.9%"
SLA: legal contract            "guarantee 99.5% (lower than SLO = safety margin)"

Error budget = 100% − SLO%
  99.9% SLO → 0.1% budget → ~43 min downtime per 30 days
  99.99% SLO → 0.01% budget → ~4 min downtime per 30 days
```

**Error budget gates:**
- > 50% remaining: ship features confidently
- 25–50%: slow down risky changes, review what's burning budget
- < 25%: feature freeze, reliability work only

---

## Resilience Patterns

| Pattern | Problem it solves | Implementation |
|---------|------------------|----------------|
| **Circuit Breaker** | Cascading failure when downstream is degraded | CLOSED → threshold errors → OPEN → probe → CLOSED |
| **Bulkhead** | One bad dependency consumes all threads | Separate connection pools per downstream |
| **Retry + jitter** | Transient failures | Exponential backoff, cap at max, add random jitter |
| **Timeout** | Hanging requests block threads | Downstream timeout < upstream timeout always |
| **Rate limiting** | Thundering herd, DDoS | Token bucket or leaky bucket at API gateway |

---

## Retry with Exponential Backoff + Jitter

```python
import random, time

def retry(fn, max_attempts=5, base=1.0, cap=32.0):
    for attempt in range(max_attempts):
        try:
            return fn()
        except TransientError:
            if attempt == max_attempts - 1:
                raise
            sleep = min(cap, base * 2 ** attempt)
            time.sleep(sleep + random.uniform(0, sleep * 0.1))  # full jitter
```

Fixed-delay retry synchronises retries across clients → thundering herd.
Jitter spreads them out → smooth recovery.

---

## CAP / PACELC Decision

```
Partition occurs?
  Choose Consistency (CP): banking, inventory, ticketing — double-booking is catastrophic
  Choose Availability (AP): social feeds, analytics, search — stale data OK

No partition?
  Choose Latency (L): user-facing reads, search, recommendations
  Choose Consistency (C): financial writes, leader election, distributed locks
```

Most databases let you tune consistency per operation (Cassandra consistency levels, Cosmos DB levels).

---

## Caching Hierarchy

```
Request → CDN (edge, static/semi-static, TTL hours)
        → App cache (Redis, session data, hot queries, TTL minutes)
        → Read replica (recent data, no cache invalidation complexity)
        → Primary DB (source of truth, writes)
```

Rules:
- Set TTL on every cache entry — never cache without expiry
- Cache invalidation strategy: TTL expiry (simple), event-driven invalidation (complex but fresh)
- Cache stampede: use lock or probabilistic early expiry on cache miss

---

## Database Scaling Patterns

| Pattern | When to use | Tradeoff |
|---------|-------------|----------|
| **Read replicas** | Read-heavy (>80% reads) | Replication lag; eventual consistency for reads |
| **Horizontal sharding** | Write throughput or table size bottleneck | Cross-shard queries expensive; choose shard key carefully |
| **Vertical partitioning** | Some columns accessed far more than others | Join complexity increases |
| **CQRS** | Read/write models need separate optimisation | At least 2× complexity; eventual consistency |

Hot partition: monitor shard distribution; use hash-based sharding to spread load evenly.

---

## Common Design Pitfalls

| Pitfall | What goes wrong | Mitigation |
|---------|-----------------|-----------|
| **SPOF** | One component down = all down | Redundancy, failover, replication |
| **Cascading failure** | Failing service overloads remaining | Circuit breaker + bulkhead |
| **Thundering herd** | Simultaneous cache miss → DB flood | Jittered retry, lock/single-flight pattern |
| **Hot partition** | Shard key creates uneven load | Hash-based sharding, monitor distribution |
| **No timeouts** | Hanging connections exhaust thread pool | Downstream timeout < upstream, always |
| **Synchronous chains** | N services × latency × failure rate compounds | Async-first for chains > 2 hops |

---

## REVIEW Checklist

| # | Check | What to look for | Report |
|---|-------|-----------------|--------|
| 1 | No SPOF | Any single component without redundancy/failover | PASS/FAIL |
| 2 | SLO defined | No SLO or error budget specified | PASS/WARN |
| 3 | Circuit breakers | External calls without circuit breaker | PASS/FAIL |
| 4 | Retry has jitter | Fixed-delay retry on external calls | PASS/FAIL |
| 5 | Timeouts set | No timeout on external calls or DB queries | PASS/FAIL |
| 6 | Stateless services | Session state stored in application memory | PASS/FAIL |
| 7 | Cache has TTL | Cached data without expiry | PASS/FAIL |
| 8 | CAP alignment | Consistency model not stated or mismatched to domain | PASS/WARN |
| 9 | Multi-AZ plan | Single datacenter for production | PASS/WARN |
| 10 | Monitoring defined | No SLI, no alert defined for the system | PASS/WARN |
