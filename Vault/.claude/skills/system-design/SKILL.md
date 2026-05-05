---
name: system-design
description: Scalable distributed systems design — load balancing, caching hierarchy, database scaling, CAP theorem, SLO/SLI/SLA, resilience patterns, and back-of-envelope estimation. Use when designing systems from scratch, reviewing architecture, or preparing for system design interviews.
argument-hint: "[design|estimate|review|patterns] [describe your system or problem]"
model: claude-sonnet-4-6
allowed-tools:
  - Read
  - Edit
  - Write
  - Bash
  - Glob
  - Grep
---

# System Design Skill

## Modes

`design` — architect a system end-to-end using the 7-step framework
`estimate` — back-of-envelope calculations: QPS, storage, bandwidth, server count
`review` — audit an existing design for SPOFs, scalability gaps, and resilience issues
`patterns` — explain or apply a specific pattern (circuit breaker, saga, CQRS, event sourcing, sharding)

## 7-Step Design Framework

1. Clarify requirements and key features
2. Identify constraints (users, QPS, storage, read/write ratio)
3. High-level architecture (components and connections)
4. Deep-dive into bottlenecks (DB, cache, network)
5. Scaling analysis (storage math, bandwidth math, server count)
6. Reliability and fault tolerance (what fails, how to recover)
7. Monitoring and alerting (SLIs to track)

## Non-Negotiable Rules

- Stateless services. All state in external stores (Redis, DB). Never in-process.
- No SPOF without a mitigation plan (redundancy, failover, replication).
- Retry with exponential backoff + jitter on all external calls. Never fixed-delay retry.
- Timeouts: downstream timeout < upstream timeout (prevent waterfall stalls).
- Circuit breaker on every external dependency (fast-fail prevents cascade).
- Multi-AZ minimum for production. Multi-region when RTO < 5 minutes.
- SLO must be set before launch. Error budget gates feature velocity.

## CAP Practical Decision

- **CP (banking, ticketing)** — double-booking is catastrophic; choose consistency
- **AP (social feeds, analytics)** — stale data better than error pages; choose availability
- **PACELC**: even without partitions, choose latency (L) vs consistency (C)

## Latency Reference Numbers

| Operation | Latency |
|-----------|---------|
| L1 cache | ~1 ns |
| Main memory | ~100 ns |
| Network datacenter | ~1 µs |
| Network cross-region | ~100 ms |
| Disk seek | ~10 ms |
| User perceptible | > 250 ms |

## Output Format

`design` → component diagram (text), data flow, bottleneck analysis, scaling path, reliability plan
`estimate` → show all math, state assumptions, final numbers
`review` → SPOF analysis, CAP alignment check, resilience pattern gaps, PASS/FAIL
`patterns` → pattern description, when to use, implementation sketch, tradeoffs

## Anti-patterns
✗ Never design a SPOF without an explicit mitigation (redundancy, failover, replication)
✗ Never use fixed-delay retry — exponential backoff + jitter always
✗ Never store session/request state in-process — external stores only
✗ Never set downstream timeout > upstream timeout — causes waterfall stalls
✗ Never launch without a defined SLO — error budget gates feature velocity

Every response ends with NEXT MOVE.