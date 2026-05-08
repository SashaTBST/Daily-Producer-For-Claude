---
name: websockets-realtime
description: Security-first realtime skill covering WebSocket servers, SSE, long-polling, collaborative state (Yjs/CRDT), Redis horizontal scaling, and game server patterns (lobby, tick, authoritative). Use when adding any form of realtime communication to a web app, API, or game server across Node.js, Bun, or Deno.
argument-hint: "[mode: guide|build|collab|scale|game|review] [transport: ws|sse|longpoll] [runtime: node|bun|deno] [description]"
---

## Scope

/websockets-realtime owns: WebSocket servers, SSE streams, long-polling, realtime state sync, CRDT/collaborative editing (Yjs), Redis pub/sub scaling, game server patterns (lobby rooms, tick loops, authoritative state, client-side prediction).

Route/middleware wiring â†’ `/node`. Database backing for realtime state â†’ `/sql` or `/nosql`. Game backend frameworks (Nakama, Colyseus) â†’ `/game-backend` (not built yet â€” flag gap).

## Transport Selection

| Signal | Pattern |
|---|---|
| Bidirectional, binary, high-frequency (>1 msg/sec) | WebSocket |
| Server-to-client only â€” notifications, dashboards, feeds | SSE |
| Collaborative editing, shared cursors | WebSocket + Yjs |
| Game server, tick loop, authoritative state | WebSocket (uWebSockets.js or Bun native) |
| Legacy clients, strict firewall / proxy environments | Long-polling |

**SSE is first-class â€” not a fallback.** Prefer SSE when communication is unidirectional.

## Runtime Routing

| Runtime | WebSocket library | SSE |
|---|---|---|
| Node.js | `ws` (simple) Â· `uWebSockets.js` (10K+ conns) Â· `Socket.IO` (rooms/namespaces/auto-fallback) | `res.write()` + `Content-Type: text/event-stream` |
| Bun | `Bun.serve()` native WebSocket | `Response` + `ReadableStream` |
| Deno | `Deno.upgradeWebSocket()` | `Response` + `ReadableStream` |

Socket.IO: use when rooms, namespaces, or auto-fallback are required. Adds ~30KB overhead â€” not for high-perf game servers.

## Modes

**GUIDE** â€” Transport or library unspecified. Ask two questions (stop at first decisive answer):
1. "Bidirectional or server-to-client only?"
2. "Concurrent connections expected: <1K / 1Kâ€“10K / 10K+?"

**BUILD** â€” Transport and runtime specified. Generate full server + client in one response. Full patterns â†’ REFERENCE.md. End: propose `/node` to wire into app.

**COLLAB** â€” Collaborative state required. Default: Yjs + y-websocket provider. Automerge for JSON-heavy state machines. Full setup â†’ REFERENCE.md.

**SCALE** â€” Horizontal scaling. Socket.IO â†’ sharded Redis adapter (Redis 7.0+). Raw ws â†’ custom Redis pub/sub broadcast. Full patterns â†’ REFERENCE.md.

**GAME** â€” Authoritative server. Tick rate table + client-side prediction + server reconciliation â†’ REFERENCE.md. Never trust client-reported positions.

**REVIEW** â€” Audit existing realtime code. Full checklist â†’ REFERENCE.md. Report each item: PASS / WARN / FAIL.

## Security Gate

Every BUILD response ends with this line â€” never skip:
`Security pass: âœ“ origin validation on WS upgrade âœ“ auth token verified before connection accepted âœ“ message size limits set âœ“ rate limiting on message handlers âœ“ no client-trusted position/state in game mode âœ“ CORS restricted on SSE endpoints`

Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration

- `/node` â€” wire WebSocket/SSE handlers into server routes and middleware
- `/nosql` â€” Redis pub/sub for scaling; room/session state in Redis cache
- `/auth` â€” authenticate on WS upgrade; JWT verification before accepting connection
- `/game-backend` â€” game framework layer (Nakama/Colyseus) â€” not built yet, flag gap
- `/backend` â€” routes here when realtime is the task layer
- `/tdd` â€” propose after BUILD to cover connection lifecycle, message handling, disconnect cleanup

## Anti-patterns

âœ— Never trust client-reported game state â€” server is always authoritative
âœ— Never skip origin validation on WS upgrade â€” open relay attack vector
âœ— Never accept a WebSocket connection before authenticating â€” auth on upgrade, not after
âœ— Never broadcast full state to all clients â€” diff and send only what changed
âœ— Never store room/realtime state in process memory only â€” Redis or DB for multi-instance
âœ— Never use long-polling as a first choice â€” legacy/firewall-constrained environments only
âœ— Never use Socket.IO for high-perf game servers â€” use uWebSockets.js or Bun native
âœ— Never skip message size limits â€” unbounded messages enable memory exhaustion


## QA
Before closing this skill session:
- [ ] Mode/language/framework confirmed with operator if ambiguous
- [ ] Output matches stated intent (code runs, no placeholders)
- [ ] Security surface reviewed (injection, auth, input validation)
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.