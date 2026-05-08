---
name: game-backend
description: Game backend skill covering Colyseus (browser/web games) and Nakama (full-stack social/competitive), ELO/Glicko-2 matchmaking, Redis leaderboards, and game services (social, tournaments, currency). Use when adding multiplayer infrastructure, matchmaking, leaderboards, or social features to any game project. For UE5, positions Node as an external microservice.
argument-hint: "[mode: guide|build|matchmaking|leaderboard|social|review] [framework: colyseus|nakama|custom] [description]"
---

## Scope

/game-backend owns: framework setup (Colyseus, Nakama), room/session management, matchmaking (ELO, Glicko-2), leaderboards, social features (friends, chat, presence), game services (tournaments, in-game currency, inventory).

Realtime tick loops and authoritative state → `/websockets-realtime`. Route/middleware wiring → `/node`. Database → `/sql` or `/nosql`. Player auth → `/auth` (always propose before BUILD).

**UE5:** Node game-backend = external microservice communicating via HTTP/WebSocket. Not an in-engine runtime — no native JS in UE5 dedicated servers.

⚠ Hathora ceased operations 2026-05-05. Do not recommend it.

## Framework Selection

| Signal | Framework |
|---|---|
| Browser/web game, fast prototype, TypeScript-first | Colyseus v0.17 |
| Social game, leaderboards + tournaments out-of-box | Nakama |
| PC/mobile game needing matchmaking + social at scale (1K+ CCU) | Nakama |
| UE5 game needing external matchmaking / leaderboard service | Nakama (HTTP microservice) |
| Full control, no framework overhead, custom protocol | Custom (`/websockets-realtime` + Redis) |

Colyseus = JS/TypeScript, room auto-sync, ~7K weekly npm downloads. Default for browser/web games.
Nakama = Go backend, full feature set, proven at 2M CCU. Default for competitive/social games.

## Modes

**GUIDE** — Framework unspecified. Ask two questions (stop at first decisive answer):
1. "Browser/web game or PC/mobile/UE5?"
2. "Need leaderboards, social features, or tournaments out-of-box?"

**BUILD** — Framework named. Generate: server setup + room/session + client SDK wiring. All files in one response. Full patterns → REFERENCE.md. End: propose `/auth` for player auth layer.

**MATCHMAKING** — Generate matchmaking system. Default: ELO (simplest, proven for indie). Glicko-2 when rating uncertainty/volatility matters. Full algorithms + K-factor table → REFERENCE.md.

**LEADERBOARD** — Redis sorted sets. Global + time-scoped (daily/weekly/all-time). Nakama built-in variant also covered. Full patterns → REFERENCE.md.

**SOCIAL** — Nakama social layer: friends, presence, chat, notifications. Full patterns → REFERENCE.md. For Colyseus projects needing social: recommend adding Nakama alongside or switching frameworks.

**REVIEW** — Audit existing game backend. Checklist → REFERENCE.md. Report each: PASS / WARN / FAIL.

## Security Gate

Every BUILD response ends with this line — never skip:
`Security pass: ✓ player auth verified before room join ✓ server-side game state authoritative ✓ input validated server-side ✓ rate limiting on matchmaking requests ✓ leaderboard scores validated server-side ✓ no client-trusted currency or inventory values ✓ Nakama admin API firewalled`

Flag any item that cannot be confirmed. Stop and fix.

## Cross-Skill Integration

- `/websockets-realtime` — tick loop, SSE, authoritative state; /game-backend sits above this layer
- `/auth` — player auth; always propose before or alongside BUILD
- `/nosql` — Redis leaderboards, matchmaking queue, room/session state
- `/node` — HTTP server wiring; external microservice layer for UE5
- `/backend` — routes here when game backend is the task layer
- `/tdd` — propose after BUILD: room lifecycle, player join/leave, matchmaking queue, leaderboard writes

## Anti-patterns

✗ Never trust client-reported scores, positions, or currency — compute and validate server-side
✗ Never recommend Hathora — platform ceased operations 2026-05-05
✗ Never embed Node.js as in-engine UE5 runtime — external microservice only
✗ Never store matchmaking queue in process memory — Redis for distributed state
✗ Never allow room join before auth — authenticate first, connect second
✗ Never use Colyseus for 1K+ CCU social games — Nakama at that scale
✗ Never implement ELO without K-factor tuning — see REFERENCE.md for correct values
✗ Never expose Nakama admin API publicly — internal/firewall only
