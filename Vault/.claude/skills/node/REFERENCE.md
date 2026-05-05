# /node — Reference

## Runtime Comparison

| Runtime | Throughput | Best for | Limits |
|---------|-----------|---------|--------|
| Node.js LTS (22/24) | ~13k req/s | Production stability, ecosystem breadth | Slower cold start vs Bun |
| Bun | ~52k req/s | Performance-critical services, dev speed | Younger ecosystem, some Node API gaps |
| Deno | ~22k req/s | Security-first, TypeScript-native, edge | Smaller package ecosystem |

Node.js: `engines: { "node": ">=22.0.0" }` in package.json.
Bun: `bun init` — no package.json type field needed (ESM native).
Deno: `deno.json` config — imports via URL or `npm:` specifier.

## Framework Decision Matrix

| Framework | Throughput | Use when | Avoid when |
|-----------|-----------|---------|-----------|
| Fastify | ~76k req/s | New API, microservice, typed routes | Existing Express codebase |
| Express | ~38k req/s | MVP, existing Express project | Starting fresh with no legacy |
| NestJS | ~28k req/s | Enterprise, large team, DI required | Operator is non-dev — complexity too high |

NestJS + Fastify adapter: `NestFactory.create<NestFastifyApplication>(AppModule, new FastifyAdapter())` — best of both (~72k req/s).

## Prisma Security Patterns

### Safe query — tagged template (always use this)
```ts
const user = await prisma.$queryRaw`SELECT * FROM users WHERE id = ${userId}`;
```

### Unsafe — never do this
```ts
// SQL injection vulnerability — $queryRawUnsafe with string concat
const user = await prisma.$queryRaw(`SELECT * FROM users WHERE id = ${userId}`);
```

### Input validation before every write
```ts
import { z } from 'zod';
const schema = z.object({ email: z.string().email(), name: z.string().min(1) });
const validated = schema.parse(req.body); // throws ZodError if invalid
await prisma.user.create({ data: validated });
```

### Connection pooling
Set `connection_limit` in DATABASE_URL or use Prisma Accelerate for production:
`postgresql://user:pass@host/db?connection_limit=5`

## SSRF Prevention

Validate all user-supplied URLs before making outbound requests:
```ts
const { hostname } = new URL(userUrl); // throws on malformed URL
const blocked = ['localhost', '127.0.0.1', '0.0.0.0', '::1'];
if (blocked.includes(hostname)) throw new Error('SSRF: internal address blocked');
// Also block RFC 1918 ranges (10.x, 172.16–31.x, 192.168.x) in production
```

## Global Error Handler Patterns

**Fastify:**
```ts
app.setErrorHandler((error, request, reply) => {
  request.log.error(error);
  reply.status(500).send({ error: 'Internal server error' }); // never leak error.message
});
```

**Express:**
```ts
app.use((err: Error, req: Request, res: Response, next: NextFunction) => {
  console.error(err);
  res.status(500).json({ error: 'Internal server error' });
});
```

## Prisma Mock Pattern (Vitest)

```ts
import { vi, expect } from 'vitest';

vi.mock('@prisma/client', () => ({
  PrismaClient: vi.fn().mockImplementation(() => ({
    user: {
      findUnique: vi.fn(),
      create: vi.fn(),
    },
  })),
}));

// In test:
import { PrismaClient } from '@prisma/client';
const prismaMock = new PrismaClient();
(prismaMock.user.findUnique as ReturnType<typeof vi.fn>).mockResolvedValue({ id: 1, email: 'test@example.com' });
```

## env File Setup

Node 20.6+: `node --env-file=.env server.js` — no package needed.
Older Node / Bun: `npm install dotenv` → `import 'dotenv/config';` at top of entry file.
Always add `.env` to `.gitignore`. Commit `.env.example` with placeholder values only.

```
# .env.example
DATABASE_URL=postgresql://user:pass@localhost:5432/mydb
PORT=3000
```

## Security Review Checklist

Report each: PASS / WARN / FAIL.

| # | Check | FAIL condition |
|---|-------|---------------|
| 1 | Raw query injection | `$queryRawUnsafe` with string concat found |
| 2 | Input validation | User input reaches DB/external call without validation |
| 3 | Secrets management | Secrets hardcoded in source — not in `process.env` |
| 4 | Error leakage | Stack traces or `error.message` returned to client |
| 5 | Mass assignment | `req.body` spread directly to DB write without validation |
| 6 | Dependency audit | `npm audit` reports high/critical vulnerabilities |
| 7 | SSRF risk | User-supplied URLs fetched without hostname validation |
| 8 | Auth middleware | Protected routes have no auth check |
| 9 | Rate limiting | No rate limiter on public endpoints |
| 10 | Env file committed | `.env` not in `.gitignore` |

## Research Sources

1. https://nodejs.org/en/about/previous-releases — Node.js LTS schedule
2. https://dev.to/jsgurujobs/bun-vs-deno-vs-nodejs-in-2026-benchmarks-code-and-real-numbers-2l9d — Runtime benchmarks 2026
3. https://betterstack.com/community/guides/scaling-nodejs/nodejs-vs-deno-vs-bun/ — Runtime comparison
4. https://www.index.dev/skill-vs-skill/backend-nestjs-vs-expressjs-vs-fastify — Framework comparison
5. https://leapcail.io/blog/fortifying-node-js-applications-against-owasp-top-10-threats — Node OWASP top 10
6. https://www.prisma.io/docs/orm/more/best-practices — Prisma best practices
7. https://www.pkgpulse.com/blog/node-test-vs-vitest-vs-jest-native-test-runner-2026 — Testing comparison 2026
