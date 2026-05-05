---
name: websockets-realtime
description: Security-first realtime skill covering WebSocket servers, SSE, long-polling, collaborative state (Yjs/CRDT), Redis horizontal scaling, and game server patterns (lobby, tick, authoritative). Use when adding any form of realtime communication to a web app, API, or game server across Node.js, Bun, or Deno.
argument-hint: "[mode: guide|build|collab|scale|game|review] [transport: ws|sse|longpoll] [runtime: node|bun|deno] [description]"
---

## Scope

/websockets-realtime owns: WebSocket servers, SSE streams, long-polling, realtime state sync, CRDT/collaborative editing (Yjs), Redis pub/sub scaling, game server patterns (lobby rooms, tick loops, authoritative state, client-side prediction).

Route/middleware wiring → `/node`. Database backing for realtime state → `/sql` or `/nosql`. Game backend frameworks (Nakama, Colyseus) → `/game-backend` (not built yet — flag gap).

## Transport Selection

| Signal | Pattern |
|---|---|
| Bidirectional, binary, high-frequency (>1 msg/sec) | WebSocket |
| Server-to-client only — notifications, dashboards, feeds | SSE |
| Collaborative editing, shared cursors | WebSocket + Yjs |
| Game server, tick loop, authoritative state | WebSocket (uWebSockets.js or Bun native) |
| Legacy clients, strict firewall / proxy environments | Long-polling |

**SSE is first-class — not a fallback.** Prefer SSE when communication is unidirectional.

## Runtime Routing

| Runtime | WebSocket library | SSE |
|---|---|---|
| Node.js | `ws` (simple) · `uWebSockets.js` (10K+ conns) · `Socket.IO` (rooms/namespaces/auto-fallback) | `res.write()` + `Content-Type: text/event-stream` |
| Bun | `Bun.serve()` native WebSocket | `Response` + `ReadableStream` |
| Deno | `Deno.upgradeWebSocket()` | `Response` + `ReadableStream` |

Socket.IO: use when rooms, namespaces, or auto-fallback are required. Adds ~30KB overhead — not for high-perf game servers.

## Modes

**GUIDE** — Transport or library unspecified. Ask two questions (stop at first decisive answer):
1. "Bidirectional or server-to-client only?"
2. "Concurrent connections expected: <1K / 1K–10K / 10K+?"

**BUILD** — Transport and runtime specified. Generate full server + client in one response. Full patterns → REFERENCE.md. End: propose `/node` to wire into app.

**COLLAB** — Collaborative state required. Default: Yjs + y-websocket provider. Automerge for JSON-heavy state machines. Full setup → REFERENCE.md.

**SCALE** — Horizontal scaling. Socket.IO → sharded Redis adapter (Redis 7.0+). Raw ws → custom Redis pub/sub broadcast. Full patterns → REFERENCE.md.

**GAME** — Authoritative server. Tick rate table + client-side prediction + server reconciliation → REFERENCE.md. Never trust client-reported positions.

**REVIEW** — Audit existing realtime code. Full checklist → REFERENCE.md. Report each item: PASS / WARN / FAIL.

## Security Gate

Every BUILD response ends with this line — never skip:
`Security pass: ✓ origin validation on WS upgrade ✓ auth token verified before connection accepted ✓ message size limits set ✓ rate limiting on message handlers ✓ no client-trusted position/state in game mode ✓ CORS restricted on SSE endpoints`

Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration

- `/node` — wire WebSocket/SSE handlers into server routes and middleware
- `/nosql` — Redis pub/sub for scaling; room/session state in Redis cache
- `/auth` — authenticate on WS upgrade; JWT verification before accepting connection
- `/game-backend` — game framework layer (Nakama/Colyseus) — not built yet, flag gap
- `/backend` — routes here when realtime is the task layer
- `/tdd` — propose after BUILD to cover connection lifecycle, message handling, disconnect cleanup

## Anti-patterns

✗ Never trust client-reported game state — server is always authoritative
✗ Never skip origin validation on WS upgrade — open relay attack vector
✗ Never accept a WebSocket connection before authenticating — auth on upgrade, not after
✗ Never broadcast full state to all clients — diff and send only what changed
✗ Never store room/realtime state in process memory only — Redis or DB for multi-instance
✗ Never use long-polling as a first choice — legacy/firewall-constrained environments only
✗ Never use Socket.IO for high-perf game servers — use uWebSockets.js or Bun native
✗ Never skip message size limits — unbounded messages enable memory exhaustion


Every response ends with NEXT MOVE.