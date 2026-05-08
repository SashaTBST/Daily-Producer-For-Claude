---
name: game-backend
description: Game backend skill covering Colyseus (browser/web games) and Nakama (full-stack social/competitive), ELO/Glicko-2 matchmaking, Redis leaderboards, and game services (social, tournaments, currency). Use when adding multiplayer infrastructure, matchmaking, leaderboards, or social features to any game project. For UE5, positions Node as an external microservice.
argument-hint: "[mode: guide|build|matchmaking|leaderboard|social|review] [framework: colyseus|nakama|custom] [description]"
---

## Scope

/game-backend owns: framework setup (Colyseus, Nakama), room/session management, matchmaking (ELO, Glicko-2), leaderboards, social features (friends, chat, presence), game services (tournaments, in-game currency, inventory).

Realtime tick loops and authoritative state â†’ `/websockets-realtime`. Route/middleware wiring â†’ `/node`. Database â†’ `/sql` or `/nosql`. Player auth â†’ `/auth` (always propose before BUILD).

**UE5:** Node game-backend = external microservice communicating via HTTP/WebSocket. Not an in-engine runtime â€” no native JS in UE5 dedicated servers.

âš  Hathora ceased operations 2026-05-05. Do not recommend it.

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

**GUIDE** â€” Framework unspecified. Ask two questions (stop at first decisive answer):
1. "Browser/web game or PC/mobile/UE5?"
2. "Need leaderboards, social features, or tournaments out-of-box?"

**BUILD** â€” Framework named. Generate: server setup + room/session + client SDK wiring. All files in one response. Full patterns â†’ REFERENCE.md. End: propose `/auth` for player auth layer.

**MATCHMAKING** â€” Generate matchmaking system. Default: ELO (simplest, proven for indie). Glicko-2 when rating uncertainty/volatility matters. Full algorithms + K-factor table â†’ REFERENCE.md.

**LEADERBOARD** â€” Redis sorted sets. Global + time-scoped (daily/weekly/all-time). Nakama built-in variant also covered. Full patterns â†’ REFERENCE.md.

**SOCIAL** â€” Nakama social layer: friends, presence, chat, notifications. Full patterns â†’ REFERENCE.md. For Colyseus projects needing social: recommend adding Nakama alongside or switching frameworks.

**REVIEW** â€” Audit existing game backend. Checklist â†’ REFERENCE.md. Report each: PASS / WARN / FAIL.

## Security Gate

Every BUILD response ends with this line â€” never skip:
`Security pass: âœ“ player auth verified before room join âœ“ server-side game state authoritative âœ“ input validated server-side âœ“ rate limiting on matchmaking requests âœ“ leaderboard scores validated server-side âœ“ no client-trusted currency or inventory values âœ“ Nakama admin API firewalled`

Flag any item that cannot be confirmed. Stop and fix.

## Cross-Skill Integration

- `/websockets-realtime` â€” tick loop, SSE, authoritative state; /game-backend sits above this layer
- `/auth` â€” player auth; always propose before or alongside BUILD
- `/nosql` â€” Redis leaderboards, matchmaking queue, room/session state
- `/node` â€” HTTP server wiring; external microservice layer for UE5
- `/backend` â€” routes here when game backend is the task layer
- `/tdd` â€” propose after BUILD: room lifecycle, player join/leave, matchmaking queue, leaderboard writes

## Anti-patterns

âœ— Never trust client-reported scores, positions, or currency â€” compute and validate server-side
âœ— Never recommend Hathora â€” platform ceased operations 2026-05-05
âœ— Never embed Node.js as in-engine UE5 runtime â€” external microservice only
âœ— Never store matchmaking queue in process memory â€” Redis for distributed state
âœ— Never allow room join before auth â€” authenticate first, connect second
âœ— Never use Colyseus for 1K+ CCU social games â€” Nakama at that scale
âœ— Never implement ELO without K-factor tuning â€” see REFERENCE.md for correct values
âœ— Never expose Nakama admin API publicly â€” internal/firewall only


## QA
Before closing this skill session:
- [ ] Mode/language/framework confirmed with operator if ambiguous
- [ ] Output matches stated intent (code runs, no placeholders)
- [ ] Security surface reviewed (injection, auth, input validation)
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.