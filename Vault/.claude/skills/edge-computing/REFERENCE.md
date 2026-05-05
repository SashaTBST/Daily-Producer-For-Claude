# edge-computing — Reference

## Platform Comparison

| | Cloudflare Workers | Deno Deploy | Vercel Edge | CloudFront Functions | Lambda@Edge |
|---|---|---|---|---|---|
| Cold start | <1ms | <5ms | ~5ms | <1ms | 250–1200ms |
| Global POPs | 300+ | ~35 | ~100 | 225+ | 13 regions |
| KV storage | ✅ Workers KV | ✅ Deno KV | ✅ Edge Config | ❌ | ❌ |
| Stateful (DO) | ✅ Durable Objects | ❌ | ❌ | ❌ | ❌ |
| SQLite at edge | ✅ D1 + DO SQLite | ❌ | ❌ | ❌ | ❌ |
| AI inference | ✅ Workers AI | ❌ (no GPU) | ❌ | ❌ | ❌ |
| Max CPU time | 30s (paid) | 50ms–10s | 25s (pro) | 2ms | 5–30s |
| Best for | General edge default | GitHub-native, Deno | Next.js only | Simple AWS transforms | Legacy AWS complex |

---

## BUILD — Cloudflare Worker

```bash
npm create cloudflare@latest my-worker
```

```ts
// src/index.ts
export default {
  async fetch(request: Request, env: Env, ctx: ExecutionContext): Promise<Response> {
    const url = new URL(request.url)

    // Auth at edge — verify JWT before origin
    const token = request.headers.get('Authorization')?.replace('Bearer ', '')
    if (!token) return new Response('Unauthorized', { status: 401 })

    // KV read
    const cached = await env.MY_KV.get(url.pathname)
    if (cached) {
      return new Response(cached, {
        headers: {
          'Content-Type': 'application/json',
          'Cache-Control': 'public, max-age=60',
          'Access-Control-Allow-Origin': env.ALLOWED_ORIGIN, // explicit CORS
        },
      })
    }

    // Fetch from origin
    const response = await fetch(`${env.ORIGIN_URL}${url.pathname}`, { request })
    const body = await response.text()

    // Cache in KV (waitUntil — non-blocking)
    ctx.waitUntil(env.MY_KV.put(url.pathname, body, { expirationTtl: 60 }))

    return new Response(body, response)
  },
}

interface Env {
  MY_KV: KVNamespace
  ORIGIN_URL: string
  ALLOWED_ORIGIN: string
}
```

```toml
# wrangler.toml
name = "my-worker"
main = "src/index.ts"
compatibility_date = "2026-01-01"

kv_namespaces = [{ binding = "MY_KV", id = "YOUR_KV_ID" }]

[vars]
ORIGIN_URL = "https://api.example.com"

# Secrets via: wrangler secret put ALLOWED_ORIGIN
```

---

## BUILD — Durable Objects (Stateful Edge)

```ts
// Durable Object — SQLite storage (GA Jan 2026)
export class RateLimiter {
  private sql: SqlStorage

  constructor(state: DurableObjectState) {
    this.sql = state.storage.sql
    this.sql.exec(`CREATE TABLE IF NOT EXISTS hits (
      key TEXT PRIMARY KEY,
      count INTEGER DEFAULT 0,
      window_start INTEGER DEFAULT 0
    )`)
  }

  async fetch(request: Request): Promise<Response> {
    const { key, limit, windowMs } = await request.json<{ key: string; limit: number; windowMs: number }>()
    const now = Date.now()

    const row = this.sql.exec<{ count: number; window_start: number }>(
      'SELECT count, window_start FROM hits WHERE key = ?', key
    ).one()

    if (!row || now - row.window_start > windowMs) {
      this.sql.exec('INSERT OR REPLACE INTO hits VALUES (?, 1, ?)', key, now)
      return Response.json({ allowed: true, remaining: limit - 1 })
    }

    if (row.count >= limit) return Response.json({ allowed: false, remaining: 0 })
    this.sql.exec('UPDATE hits SET count = count + 1 WHERE key = ?', key)
    return Response.json({ allowed: true, remaining: limit - row.count - 1 })
  }
}
```

---

## BUILD — Deno Deploy

```ts
// main.ts
Deno.serve(async (req: Request): Promise<Response> => {
  const url = new URL(req.url)

  if (req.method === 'OPTIONS') {
    return new Response(null, {
      headers: {
        'Access-Control-Allow-Origin': Deno.env.get('ALLOWED_ORIGIN')!,
        'Access-Control-Allow-Methods': 'GET, POST',
        'Access-Control-Allow-Headers': 'Content-Type, Authorization',
      },
    })
  }

  // Web Crypto — only safe crypto at edge
  const key = await crypto.subtle.importKey('raw', /* ... */, { name: 'HMAC', hash: 'SHA-256' }, false, ['verify'])

  return Response.json({ path: url.pathname })
})
```

---

## BUILD — CloudFront Functions (AWS)

```js
// handler.js — Simple A/B redirect (no network access, 2ms CPU max)
async function handler(event) {
  const request = event.request
  const uri = request.uri

  // Normalise trailing slash
  if (uri.endsWith('/') && uri !== '/') {
    return { statusCode: 301, headers: { location: { value: uri.slice(0, -1) } } }
  }

  // A/B test via cookie
  const cookie = request.cookies?.experiment?.value
  if (!cookie) {
    request.headers['x-experiment'] = { value: Math.random() < 0.5 ? 'A' : 'B' }
  }

  return request
}
```

---

## Workers AI — Edge Inference

```ts
// AI inference at edge (no origin round-trip)
export default {
  async fetch(request: Request, env: Env): Promise<Response> {
    const { prompt } = await request.json<{ prompt: string }>()

    const result = await env.AI.run('@cf/meta/llama-3.1-8b-instruct', {
      messages: [{ role: 'user', content: prompt }],
      max_tokens: 256,
    })

    return Response.json(result)
  },
}

interface Env { AI: Ai }
```

```toml
# wrangler.toml addition
[ai]
binding = "AI"
```

---

## REVIEW — Security Checklist

| Check | Pass condition |
|---|---|
| No Node.js APIs | `fs`, `path`, `require` absent; Web Crypto used for crypto ops |
| Secrets via env vars | No hardcoded keys; `wrangler secret put` or env dashboard used |
| CORS headers explicit | `Access-Control-Allow-Origin` set to specific origin, not `*` |
| Input validation | Request body/params bounded and sanitised before processing |
| Rate limiting | KV counter or Durable Object rate limiter wired |
| No sensitive data in KV keys | KV keys are opaque IDs, not PII |
| D1/DO SQLite queries parameterised | No string interpolation in SQL |
| WinterTC assumption absent | No cross-platform assumptions; platform-specific code tested |
