---
name: tdd-reference
description: Extended reference for /tdd — per-cycle checklist, extended test passes, and mocking rules.
type: reference
---

## Per-Cycle Checklist

Run this after each RED → GREEN cycle before moving to the next behaviour:

```
[ ] Test describes behaviour, not implementation
[ ] Test uses public interface only
[ ] Test would survive an internal refactor
[ ] Code is minimal for this test
[ ] No speculative features added
```

---

## Extended Test Passes

Run these after functional green. Select passes relevant to what the slice touches — not all apply to every slice.

**Security pass** (auth, input handling, data access, APIs)
- Authentication: is access gated correctly?
- Authorisation: can a user access another user's data? Are role boundaries enforced?
- Input validation: are all inputs sanitised? Injection vectors blocked (SQL, XSS, command)?
- Output encoding: is sensitive data masked or excluded from responses?

**Storage pass** (databases, files, caches, migrations)
- Persistence: does state survive a restart?
- Migration safety: runs forward cleanly? Can it be reversed without data loss?
- Concurrency: race conditions under simultaneous writes?
- Integrity: are constraints enforced? Can invalid data states be reached?

**Commercial pass** (pricing, billing, permissions, subscriptions, business rules)
- Pricing logic: are rates calculated correctly? Edge cases at tier boundaries?
- Billing: are charges triggered at the right events? No double charges?
- Permissions: do plan/tier restrictions correctly gate features?
- Audit trail: are commercial events logged for compliance?

**Performance pass** (critical paths, large data)
- Response time under expected load
- Memory usage under sustained operation
- Query efficiency: N+1, missing indexes, large scans

**Accessibility pass** (UI slices)
- Keyboard navigation works
- Screen reader labels on all interactive elements
- Colour contrast WCAG AA
- Error states announced, not just styled

---

## Mocking Rules

- Mock at system boundaries only (external APIs, file system, time, randomness)
- Never mock the module under test
- If a module is hard to test without mocking its internals: it is a design problem, not a test problem
- Test databases: prefer a real test database over an in-memory mock — mocked DB tests pass when real ones fail
