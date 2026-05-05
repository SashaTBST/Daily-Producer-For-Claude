# game-backend — Reference

## Framework Comparison

| | Colyseus v0.17 | Nakama | Custom |
|---|---|---|---|
| Language | TypeScript | Go (JS SDK for client) | Any |
| Room sync | Auto (defineRoomType) | Manual | Manual |
| Matchmaking | Basic (by room filter) | Full (flexible, ELO-ready) | Build it |
| Leaderboards | No | Built-in | Redis sorted sets |
| Social (friends/chat) | No | Yes | Build it |
| Tournaments | No | Yes | Build it |
| In-game currency | No | Yes (wallet) | Build it |
| UE5 integration | HTTP only | HTTP/gRPC | HTTP/WS |
| CCU sweet spot | <1K (indie/browser) | 1K–2M+ | Unlimited |
| Self-hosted | Yes | Yes | Yes |

---

## BUILD — Colyseus v0.17

```bash
npm create colyseus-app@latest my-game-server
# Or manual:
npm install colyseus @colyseus/tools
```

```ts
// server.ts — defineServer() API (v0.17)
import { defineServer, Room, Client } from 'colyseus'
import { Schema, type, MapSchema } from '@colyseus/schema'

class PlayerState extends Schema {
  @type('number') x: number = 0
  @type('number') y: number = 0
}

class GameState extends Schema {
  @type({ map: PlayerState }) players = new MapSchema<PlayerState>()
}

class GameRoom extends Room<GameState> {
  onCreate() {
    this.setState(new GameState())
    this.onMessage('move', (client, data) => {
      const player = this.state.players.get(client.sessionId)
      if (!player) return
      // Validate input server-side — never trust raw coords
      player.x = Math.max(0, Math.min(1000, data.x))
      player.y = Math.max(0, Math.min(1000, data.y))
    })
  }
  onJoin(client: Client) {
    this.state.players.set(client.sessionId, new PlayerState())
  }
  onLeave(client: Client) {
    this.state.players.delete(client.sessionId)
  }
}

export default defineServer({
  options: { presence: new RedisPresence() }, // required for multi-instance
  initializeGameServer: (gameServer) => gameServer.define('game', GameRoom),
  initializeExpress: (app) => { /* REST endpoints */ },
})
```

```ts
// Client (browser)
import Colyseus from 'colyseus.js'
const client = new Colyseus.Client('ws://localhost:2567')
const room = await client.joinOrCreate<GameState>('game')
room.state.players.onAdd((player, sessionId) => { /* render */ })
```

---

## BUILD — Nakama

```bash
# Docker Compose (self-hosted)
# nakama + postgres
docker compose up -d
```

```ts
// Nakama server-side functions (TypeScript runtime)
const rpcFindMatch: nkruntime.RpcFunction = (ctx, logger, nk, payload) => {
  const matchId = nk.matchCreate('game_logic', { fast: true })
  return JSON.stringify({ matchId })
}

const matchInit: nkruntime.MatchInitFunction = (ctx, logger, nk, params) => {
  return {
    state: { players: {} },
    tickRate: 20,
    label: JSON.stringify({ mode: 'default' })
  }
}
```

```ts
// Client (JS SDK)
import { Client } from '@heroiclabs/nakama-js'
const client = new Client('defaultkey', 'localhost', '7350')
const session = await client.authenticateDevice(deviceId)
const socket = client.createSocket()
await socket.connect(session)
// Join match
const match = await socket.joinMatch(matchId)
socket.onmatchdata = (data) => { /* handle realtime state */ }
```

---

## MATCHMAKING — ELO Algorithm

ELO is the default for indie games. Simple, proven, interpretable.

**K-factor table (tune to game genre):**
| Player rating | K-factor | Use case |
|---|---|---|
| <1200 (new) | 40 | High volatility — new players settle fast |
| 1200–2000 | 20 | Standard competitive |
| >2000 (master) | 10 | High-rating stability |

```ts
function updateElo(winnerRating: number, loserRating: number, k = 20) {
  const expectedWin = 1 / (1 + Math.pow(10, (loserRating - winnerRating) / 400))
  const expectedLoss = 1 - expectedWin
  return {
    winner: Math.round(winnerRating + k * (1 - expectedWin)),
    loser: Math.round(loserRating + k * (0 - expectedLoss)),
  }
}

// Example
const result = updateElo(1400, 1600) // lower-rated upsets higher
// winner: 1424, loser: 1576
```

**Glicko-2** — use when: player plays infrequently, need rating uncertainty (RD) and volatility (σ). More complex. Nakama has built-in Glicko-2 leaderboard scoring.

**Matchmaking queue (Redis)**:
```ts
// Add player to queue
await redis.zadd('matchmaking:queue', playerElo, playerId)

// Find opponent within ±100 ELO
const [min, max] = [playerElo - 100, playerElo + 100]
const candidates = await redis.zrangebyscore('matchmaking:queue', min, max)
// Remove both from queue and create match
```

---

## LEADERBOARD — Redis Sorted Sets

O(log N) writes, O(log N + M) range reads. Atomic. No race conditions.

```ts
// Write score (atomic — safe under high concurrency)
await redis.zadd('leaderboard:global', { score: newScore, member: playerId })
// Or increment:
await redis.zincrby('leaderboard:global', delta, playerId)

// Read top 10
const top10 = await redis.zrevrange('leaderboard:global', 0, 9, 'WITHSCORES')

// Player rank (0-indexed, add 1 for display)
const rank = await redis.zrevrank('leaderboard:global', playerId)

// Time-scoped leaderboards via key namespacing
const dailyKey = `leaderboard:daily:${new Date().toISOString().slice(0, 10)}`
const weeklyKey = `leaderboard:weekly:${getWeekNumber()}`
await redis.zadd(dailyKey, { score, member: playerId })
await redis.expire(dailyKey, 86400 * 2) // TTL: 2 days after reset
```

**Nakama built-in leaderboards:**
```ts
// Server-side
nk.leaderboardCreate('weekly_score', false, 'desc', 'best', 'weekly')
nk.leaderboardRecordWrite(ctx.userId, 'weekly_score', score)
const records = nk.leaderboardRecordsList('weekly_score', [], 10)
```

---

## SOCIAL — Nakama

```ts
// Friends
await client.addFriends(session, [friendId])
const friends = await client.listFriends(session)

// Presence / online status
socket.onstatuspresence = (presences) => { /* update UI */ }
await socket.updateStatus('In game')

// Chat
const channel = await socket.joinChat(roomId, 1) // type 1 = room
socket.onchannelmessage = (msg) => console.log(msg.content)
await socket.writeChatMessage(channel.id, { text: 'gg' })
```

---

## UE5 External Microservice Pattern

UE5 dedicated servers are C++ only. Node game-backend = external microservice.

```
[UE5 Game Client]
       ↓ HTTP REST
[Node/Nakama — matchmaking, leaderboards, social]
       ↓ WebSocket
[UE5 Dedicated Server — authoritative game logic, C++]
```

Integration options:
1. **HTTP plugin** — UE5 `FHttpModule` calls Node REST API for matchmaking/leaderboard
2. **WebSocket plugin** — `IWebSocket` module for realtime state sync to Node relay
3. **NodeJs-Unreal plugin** — embeds Node.js as child process (experimental, not production)

Recommended: option 1 (HTTP) for game services, option 2 (WS) only if realtime relay is needed.

---

## REVIEW — Security Checklist

| Check | Pass condition |
|---|---|
| Auth before room join | Session token verified before `onJoin` executes |
| Server-side input validation | All client inputs bounds-checked and sanitised |
| No client-trusted state | Score, position, currency computed server-side |
| Rate limiting on matchmaking | Max N requests/min per player (Redis counter) |
| Leaderboard score validation | Score computed from server game result, not client payload |
| Nakama admin API firewalled | Port 7351 (admin) not publicly accessible |
| Redis not publicly exposed | Redis port (6379) bound to internal network only |
| Room/session cleanup | All intervals, state, and Redis keys cleaned on disconnect |
| Currency/inventory atomic | Wallet ops use Nakama wallet API or Redis transactions |
