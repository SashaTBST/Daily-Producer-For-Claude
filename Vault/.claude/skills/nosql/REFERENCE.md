# NoSQL Skill — Reference
MongoDB / Valkey (Redis-compatible) | Last verified: 2026-04-30

## Research Sources
- MongoDB aggregation pipeline: https://docs.atlas.mongodb.com/data-explorer/cloud-agg-pipeline/
- Valkey vs Redis 2026: https://dev.to/deploynix/valkey-vs-redis-for-laravel-caching-and-queues-what-you-need-to-know-3653
- NoSQL injection security: https://portswigger.net/web-security/nosql-injection
- MongoDB common mistakes: https://accuweb.cloud/blog/mongodb-community-edition-common-mistakes/
- Vector database comparison 2026: https://www.groovyweb.co/blog/vector-database-comparison-2026

---

## MongoDB — Injection Prevention

```javascript
// ✗ NEVER — unsanitized operator injection
// req.body = { "$gt": "" }  →  finds all users
const users = await User.find({ email: req.body.email });  // if body = {$gt: ""} → RCE

// ✓ Validate and sanitize input before use
import { isString, sanitize } from 'some-validation-lib';

if (!isString(req.body.email) || !req.body.email.includes('@')) {
    return res.status(400).json({ error: 'Invalid email' });
}
const users = await User.find({ email: req.body.email });

// ✓ Mongoose schema validation (blocks operator injection automatically)
const userSchema = new mongoose.Schema({
    email: { type: String, required: true, match: /^[^\s@]+@[^\s@]+\.[^\s@]+$/ }
});
```

---

## MongoDB Aggregation Pipeline

```javascript
// Standard pipeline: match early, project late
const result = await Order.aggregate([
    { $match: { status: 'completed', createdAt: { $gte: startDate } } },  // filter first
    { $lookup: {
        from: 'users',
        localField: 'userId',
        foreignField: '_id',
        as: 'user'
    }},
    { $unwind: '$user' },
    { $group: {
        _id: '$user._id',
        totalSpend: { $sum: '$total' },
        orderCount: { $sum: 1 }
    }},
    { $project: { totalSpend: 1, orderCount: 1 } },  // only return needed fields
    { $limit: 100 }  // always limit
]);
```

---

## MongoDB Schema Validation

```javascript
// $jsonSchema on collection — enforce required fields and types
db.createCollection('users', {
    validator: {
        $jsonSchema: {
            bsonType: 'object',
            required: ['email', 'name', 'createdAt'],
            properties: {
                email: {
                    bsonType: 'string',
                    pattern: '^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\\.[a-zA-Z]{2,}$'
                },
                name: { bsonType: 'string', minLength: 2 },
                createdAt: { bsonType: 'date' }
            }
        }
    },
    validationLevel: 'strict',
    validationAction: 'error'
});
```

---

## MongoDB Indexes

```javascript
// Single field
db.users.createIndex({ email: 1 }, { unique: true });

// Compound — field order matters (matches query pattern)
db.orders.createIndex({ userId: 1, createdAt: -1 });

// Partial — only index active documents (smaller, faster)
db.users.createIndex({ email: 1 }, { partialFilterExpression: { active: true } });

// TTL — auto-delete expired documents
db.sessions.createIndex({ expiresAt: 1 }, { expireAfterSeconds: 0 });
```

---

## Valkey — Cache Patterns

```python
# Cache-aside pattern (most common)
async def get_user(user_id: str) -> dict:
    cache_key = f"user:{user_id}"
    cached = await valkey.get(cache_key)
    if cached:
        return json.loads(cached)

    user = await db.users.find_one({"_id": user_id})
    if user:
        # Always set TTL — never cache without expiry
        await valkey.setex(cache_key, 3600, json.dumps(user))  # 1 hour TTL
    return user

# Rate limiting — sliding window
async def check_rate_limit(ip: str, limit: int = 100, window: int = 60) -> bool:
    key = f"ratelimit:{ip}"
    count = await valkey.incr(key)
    if count == 1:
        await valkey.expire(key, window)  # set TTL on first request
    return count <= limit
```

---

## Valkey — Safe Key Scanning

```bash
# ✗ NEVER in production — blocks server, returns all keys at once
KEYS user:*

# ✓ SCAN with cursor — non-blocking, paginated
SCAN 0 MATCH user:* COUNT 100
# Returns: [next_cursor, [key1, key2, ...]]
# Repeat with next_cursor until cursor = 0
```

---

## REVIEW Checklist

| # | Check | What to look for | Report |
|---|-------|-----------------|--------|
| 1 | No operator injection | User input passed directly as query object without type validation | PASS/FAIL |
| 2 | limit() on queries | MongoDB queries without `.limit()` on large collections | PASS/FAIL |
| 3 | TTL on cache keys | Valkey SETEX or EXPIRE used — never bare SET without TTL | PASS/FAIL |
| 4 | No KEYS * in prod | `KEYS *` or `KEYS pattern` in application code | PASS/FAIL |
| 5 | Schema validation | Collections missing `$jsonSchema` validator | PASS/WARN |
| 6 | Indexes on query fields | Fields used in `$match` or `.find()` without indexes | PASS/WARN |
| 7 | Unbounded arrays | Array fields without size cap (16MB document limit risk) | PASS/WARN |
| 8 | Auth enabled | Valkey deployed without `requirepass` / ACL configured | PASS/FAIL |
| 9 | ObjectId handling | String vs ObjectId comparison mismatches | PASS/WARN |
| 10 | Mass assignment | Client-supplied field names used in `$set` without whitelist | PASS/FAIL |
