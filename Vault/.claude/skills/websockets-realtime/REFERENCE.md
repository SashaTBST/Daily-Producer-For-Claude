# websockets-realtime — Reference

## Library Comparison

| Library | Runtime | Use case | Connections | Notes |
|---|---|---|---|---|
| `ws` | Node.js | Simple WS server, low overhead | Up to ~10K | Default choice for Node; no rooms/namespaces |
| `uWebSockets.js` | Node.js | High-perf, game servers, >10K conns | 100K+ | 10x faster than `ws`; C++ bindings; no Socket.IO compat |
| `Socket.IO` | Node.js/Bun | Rooms, namespaces, auto-fallback, broadcasting | Up to ~10K (single); scale with Redis adapter | Adds ~30KB; best for chat/collab apps |
| `Bun.serve()` | Bun | Native WS; production-ready | 100K+ | Idiomatic Bun; no third-party lib needed |
| `Deno.upgradeWebSocket()` | Deno | Native WS | High | Use inside `Deno.serve()` handler |

---

## BUILD Patterns

### Node.js — ws (simple)
```js
import { WebSocketServer } from 'ws'
const wss = new WebSocketServer({ port: 8080 })
wss.on('connection', (ws, req) => {
  // Auth on upgrade — verify token from req.headers before accepting
  ws.on('message', (data) => {
    const msg = JSON.parse(data.toString())
    // validate message size: if (data.length > MAX_SIZE) return ws.close(1009)
    ws.send(JSON.stringify({ type: 'ack' }))
  })
  ws.on('close', () => { /* cleanup */ })
})
```

### Bun — native WebSocket
```ts
Bun.serve({
  port: 8080,
  fetch(req, server) {
    if (server.upgrade(req)) return // Auth: check headers before upgrade
    return new Response('Not a WS request', { status: 400 })
  },
  websocket: {
    message(ws, data) { ws.send(data) },
    open(ws) { },
    close(ws) { },
  },
})
```

### Deno — native WebSocket
```ts
Deno.serve({ port: 8080 }, (req) => {
  const { socket, response } = Deno.upgradeWebSocket(req)
  socket.onmessage = (e) => socket.send(e.data)
  socket.onclose = () => { }
  return response
})
```

### SSE — Node.js (Express)
```js
app.get('/events', (req, res) => {
  res.setHeader('Content-Type', 'text/event-stream')
  res.setHeader('Cache-Control', 'no-cache')
  res.setHeader('Connection', 'keep-alive')
  res.setHeader('Access-Control-Allow-Origin', ALLOWED_ORIGIN) // CORS required
  const send = (data) => res.write(`data: ${JSON.stringify(data)}\n\n`)
  const interval = setInterval(() => send({ ts: Date.now() }), 1000)
  req.on('close', () => clearInterval(interval))
})
```

### SSE — Bun / Deno
```ts
// Bun / Deno: return a ReadableStream
const stream = new ReadableStream({
  start(controller) {
    const interval = setInterval(() => {
      controller.enqueue(`data: ${JSON.stringify({ ts: Date.now() })}\n\n`)
    }, 1000)
    // cleanup: no built-in close hook — wrap in abort signal
  }
})
return new Response(stream, {
  headers: { 'Content-Type': 'text/event-stream', 'Cache-Control': 'no-cache' }
})
```

---

## COLLAB — Yjs Setup

Yjs is the default for collaborative state. Used by Notion, Jupyter, and most production collab editors.

```bash
npm install yjs y-websocket
```

```js
// Server
import { WebSocketServer } from 'ws'
import { setupWSConnection } from 'y-websocket/bin/utils'

const wss = new WebSocketServer({ port: 1234 })
wss.on('connection', (ws, req) => setupWSConnection(ws, req))
```

```js
// Client
import * as Y from 'yjs'
import { WebsocketProvider } from 'y-websocket'

const doc = new Y.Doc()
const provider = new WebsocketProvider('ws://localhost:1234', 'room-name', doc)
const yText = doc.getText('shared')
yText.observe(event => console.log(yText.toString()))
```

**Automerge** — use instead of Yjs when: state is a JSON document (not a text editor), merge semantics over CRDTs matter more than performance. Avoid for large documents — memory constraints at scale.

---

## SCALE — Redis Horizontal Scaling

### Socket.IO — Sharded Redis Adapter (Redis 7.0+, recommended)
```bash
npm install @socket.io/redis-adapter ioredis
```
```js
import { createClient } from 'redis'
import { createShardedAdapter } from '@socket.io/redis-adapter'

const pubClient = createClient({ url: 'redis://localhost:6379' })
const subClient = pubClient.duplicate()
await Promise.all([pubClient.connect(), subClient.connect()])

io.adapter(createShardedAdapter(pubClient, subClient))
```
Sharded adapter reduces pub/sub fanout at 100K+ connections vs classic adapter.

### Raw ws — Custom Redis Broadcasting
```js
import { createClient } from 'redis'
const pub = createClient()
const sub = pub.duplicate()
await Promise.all([pub.connect(), sub.connect()])

// On message received from a client:
await pub.publish('broadcast-channel', JSON.stringify(msg))

// Each instance subscribes:
await sub.subscribe('broadcast-channel', (message) => {
  wss.clients.forEach(client => {
    if (client.readyState === WebSocket.OPEN) client.send(message)
  })
})
```

---

## GAME — Authoritative Server Patterns

### Tick Rate Reference

| Genre | Tick rate | Notes |
|---|---|---|
| Competitive FPS | 64–128 Hz | Valve CS2: 64Hz standard, 128Hz premium |
| Action / battle royale | 30–60 Hz | Fortnite: 30Hz; Apex: 60Hz |
| Real-time strategy | 20–30 Hz | |
| Turn-based | 1 Hz or event-driven | No fixed tick needed |
| Casual web game | 20 Hz | Sufficient for most browser games |

### Authoritative Tick Loop (Node.js)
```js
const TICK_RATE = 20 // Hz
const TICK_MS = 1000 / TICK_RATE
const gameState = new Map() // roomId → state

setInterval(() => {
  for (const [roomId, state] of gameState) {
    // 1. Process buffered inputs (never trust positions)
    // 2. Simulate physics / game logic
    // 3. Compute delta (only changed entities)
    // 4. Broadcast delta to room clients
    broadcastDelta(roomId, computeDelta(state))
  }
}, TICK_MS)
```

### Client-Side Prediction + Server Reconciliation
```js
// Client: apply input locally, store in pending buffer
pendingInputs.push({ seq: inputSeq++, input, timestamp: Date.now() })
applyInputLocally(input)

// Server: process input, return ack with seq + authoritative state
// Client on receiving ack:
pendingInputs = pendingInputs.filter(i => i.seq > ack.lastProcessedSeq)
state = ack.authoritativeState
pendingInputs.forEach(i => applyInputLocally(i.input)) // re-apply unconfirmed
```

Reference: Gabriel Gambetta's [Fast-Paced Multiplayer](https://www.gabrielgambetta.com/client-server-game-architecture.html)

---

## REVIEW — Security Checklist

| Check | Pass condition |
|---|---|
| Origin validation on WS upgrade | Server checks `Origin` header against allowlist before accepting |
| Auth before connection accepted | JWT/session verified in upgrade handler, connection rejected on fail |
| Message size limits | Max frame size configured (ws: `maxPayload`; uWS: `maxPayloadLength`) |
| Rate limiting on message handlers | Per-connection message rate tracked; excess → close(1008) |
| No client-trusted state (game) | Server ignores client-reported positions; uses server-side simulation only |
| SSE CORS | `Access-Control-Allow-Origin` set to specific origin, not `*` |
| Room state not memory-only | Room/session state persisted to Redis or DB for multi-instance safety |
| Disconnect cleanup | All intervals, subscriptions, and room memberships cleaned on `close` |
| Reconnection handled | Client has exponential backoff reconnect; server handles duplicate connection IDs |
