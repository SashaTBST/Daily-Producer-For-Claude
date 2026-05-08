---
name: system-design
description: Scalable distributed systems design â€” load balancing, caching hierarchy, database scaling, CAP theorem, SLO/SLI/SLA, resilience patterns, and back-of-envelope estimation. Use when designing systems from scratch, reviewing architecture, or preparing for system design interviews.
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

`design` â€” architect a system end-to-end using the 7-step framework
`estimate` â€” back-of-envelope calculations: QPS, storage, bandwidth, server count
`review` â€” audit an existing design for SPOFs, scalability gaps, and resilience issues
`patterns` â€” explain or apply a specific pattern (circuit breaker, saga, CQRS, event sourcing, sharding)

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

- **CP (banking, ticketing)** â€” double-booking is catastrophic; choose consistency
- **AP (social feeds, analytics)** â€” stale data better than error pages; choose availability
- **PACELC**: even without partitions, choose latency (L) vs consistency (C)

## Latency Reference Numbers

| Operation | Latency |
|-----------|---------|
| L1 cache | ~1 ns |
| Main memory | ~100 ns |
| Network datacenter | ~1 Âµs |
| Network cross-region | ~100 ms |
| Disk seek | ~10 ms |
| User perceptible | > 250 ms |

## Output Format

`design` â†’ component diagram (text), data flow, bottleneck analysis, scaling path, reliability plan
`estimate` â†’ show all math, state assumptions, final numbers
`review` â†’ SPOF analysis, CAP alignment check, resilience pattern gaps, PASS/FAIL
`patterns` â†’ pattern description, when to use, implementation sketch, tradeoffs

## Anti-patterns
âœ— Never design a SPOF without an explicit mitigation (redundancy, failover, replication)
âœ— Never use fixed-delay retry â€” exponential backoff + jitter always
âœ— Never store session/request state in-process â€” external stores only
âœ— Never set downstream timeout > upstream timeout â€” causes waterfall stalls
âœ— Never launch without a defined SLO â€” error budget gates feature velocity

## QA
Before closing this skill session:
- [ ] Mode/language/framework confirmed with operator if ambiguous
- [ ] Output matches stated intent (code runs, no placeholders)
- [ ] Security surface reviewed (injection, auth, input validation)
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.