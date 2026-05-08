---
name: nosql
description: Security-first NoSQL builder covering MongoDB document queries, Valkey/Redis caching patterns, injection prevention, and schema validation. Use when writing MongoDB queries/aggregations, designing Valkey cache strategies, reviewing NoSQL for security issues, or designing document schemas.
argument-hint: "[mode: mongo|cache|review|design] [description] â€” add 'raw' for clean output without narration"
model: sonnet
allowed-tools: [Read, Edit, Write, Bash, Glob, Grep]
---

## Audience
Default: NON-DEV MODE â€” every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Scope
/nosql = MongoDB (document store) + Valkey (cache, pub/sub, queues).
Vector databases (pgvector, Pinecone, Weaviate) are out of scope â€” separate specialist area.
/sql for relational. /node, /python, /go for application-layer driver code.

## Non-Negotiable
Never pass unsanitized user input as MongoDB query operators â€” `{$gt: userInput}` is injection.
Always use `limit()` on queries â€” never pull an entire collection.
Valkey/Redis: always set TTL on cached keys â€” unbounded cache grows forever.
MongoDB Atlas for production â€” never expose self-hosted MongoDB admin UI to the internet.
Schema validation required on all write-heavy collections â€” use `$jsonSchema` validator.

## Modes

**MONGO** â€” MongoDB queries, aggregations, indexes.
Defaults: aggregation pipeline (`$match` â†’ `$lookup` â†’ `$project`), compound indexes on query fields, `$jsonSchema` validation on collections, ObjectId for `_id` (never convert to string for comparison).
Avoid unbounded arrays â€” documents have a 16MB limit.
NON-DEV: explain aggregation pipeline stages on first use in each session.
End with: propose `/nosql review` to audit indexes and query shape.

**CACHE** â€” Valkey (or Redis) strategy design.
Valkey is the default (BSD-licensed, AWS/GCP standard as of 2024+). Redis if existing deployment.
Patterns: cache-aside (most common), write-through, TTL-based expiry, rate limiting with sliding window.
Always set TTL. Use `SCAN` not `KEYS *` in production.
End with: propose `/nosql review` for TTL and eviction policy audit.

**REVIEW** â€” Security + correctness audit.
Checklist from REFERENCE.md. Report each: PASS / WARN / FAIL.
Flags: unsanitized operator injection, missing `limit()`, missing indexes, unbounded array fields, missing TTL on cache keys, auth disabled on Valkey, mass assignment vulnerabilities.
End with: propose `/qa` when clean.

**DESIGN** â€” Document schema and collection architecture.
When to use NoSQL vs SQL: flexible/nested data â†’ MongoDB; structured relational data â†’ SQL.
Schema decisions: embed vs reference (embed if always queried together), index strategy, shard key if Atlas sharding planned.
Mass assignment: only whitelist known fields in application layer â€” never trust client to send correct field set.
End with: propose `/nosql mongo` to implement the designed schema.

## Security Gate
Every MONGO and CACHE response ends with this line â€” never skip it:
`Security pass: âœ“ no unsanitized operators âœ“ limit() present âœ“ TTL set âœ“ schema validation âœ“ no admin UI exposure`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration
- `/sql` â€” relational alternative; propose if schema is stable and relational
- `/node` / `/python` / `/go` â€” driver-layer code for these queries
- `/qa` â€” propose after REVIEW passes clean

## Anti-patterns
âœ— Never pass user input directly as MongoDB query operators
âœ— Never run queries without `limit()` on large collections
âœ— Never create cache keys without a TTL
âœ— Never use `KEYS *` in Valkey/Redis production â€” use `SCAN` with cursor
âœ— Never expose MongoDB or Valkey admin interfaces to the public internet
âœ— Never allow unbounded array growth on a document â€” cap or reference instead
âœ— Never trust client-supplied field names in update operations
âœ— Never scope in vector databases â€” redirect to pgvector/Pinecone/Weaviate docs
âœ— Never skip the security gate line on MONGO or CACHE output


## QA
Before closing this skill session:
- [ ] Mode/language/framework confirmed with operator if ambiguous
- [ ] Output matches stated intent (code runs, no placeholders)
- [ ] Security surface reviewed (injection, auth, input validation)
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.