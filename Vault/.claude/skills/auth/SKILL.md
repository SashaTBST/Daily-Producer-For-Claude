---
name: auth
description: Security-first authentication skill covering OAuth2/OIDC, JWT, passkeys/WebAuthn, and session management. Guides pattern selection, builds implementations, and audits existing auth. Use when adding any form of user authentication to a web app, API, or game server.
argument-hint: "[mode: guide|build|review] [pattern: oauth2|jwt|webauthn|session] [description] â€” add 'raw' for clean code without narration"
---

## Audience
Default: NON-DEV MODE â€” every output includes plain-English explanation above code. Override: include `raw` for clean code without narration.

## Scope
/auth = auth patterns, token issuance, session management, auth module code.
Node/Bun is the primary code output. Other stacks: /auth establishes the pattern in plain English, then routes to the language skill for implementation.
WebAuthn = server side only (SimpleWebAuthn server package). Browser-side ceremony â†’ `/javascript`.
Laravel: /auth routes to `/laravel` â€” load `baseline/otp-auth.md`. Never generates password-based Laravel auth.
Route/middleware wiring â†’ `/node`. Auth requirements in API spec â†’ `/api-design`.

## Modes

**GUIDE** â€” No pattern specified, or operator unsure what they need.
Ask two questions (in order â€” stop after first if answer is decisive):
1. "Browser web app or API / mobile client?"
2. "Own user accounts, social login (Google / GitHub / etc.), or passwordless?"

| App type | Users | Pattern |
|---|---|---|
| Web app | Own accounts | Session or WebAuthn |
| Web app | Social login | OAuth2/OIDC |
| API / mobile | Own accounts | JWT |
| API / mobile | Social login | OAuth2/OIDC tokens |
| Any | Passwordless | WebAuthn |

NON-DEV default: before any code, surface hosted auth options (Auth0, Clerk, Supabase Auth) as the fastest path. Only proceed to BUILD if operator confirms self-hosted.

**BUILD** â€” Pattern is named. Generate the implementation.
Accepted patterns: `oauth2` Â· `jwt` Â· `webauthn` Â· `session`
Full build guides and library table â†’ REFERENCE.md.
Always Node/Bun primary. Fastify default; Express on request. Output all files in one response.
End with: propose `/node` to wire the auth module into server routes.

**REVIEW** â€” Audit existing auth code or config.
Full checklist â†’ REFERENCE.md. Report each item: PASS / WARN / FAIL.
End with: propose `/qa` when clean.

## Security Gate
Every BUILD response ends with this line â€” never skip it:
`Security pass: âœ“ no localStorage token storage âœ“ PKCE enforced on OAuth2 âœ“ RS256/ES256 not HS256 for multi-tenant âœ“ HttpOnly + Secure + SameSite on cookies âœ“ rate limiting on auth endpoints documented âœ“ no alg:none`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration
- `/node` â€” wire auth module into routes and middleware (/auth stops at auth module boundary)
- `/javascript` â€” browser-side WebAuthn ceremony (`navigator.credentials`)
- `/api-design` â€” define auth requirements before /auth builds them
- `/laravel` â€” Laravel auth; load `baseline/otp-auth.md`
- `/backend` â€” routes here when auth is the task layer
- `/tdd` â€” propose after BUILD to cover token issuance, validation, and expiry paths

## Anti-patterns
âœ— Never implement Implicit Flow â€” deprecated; Auth Code + PKCE only
âœ— Never store tokens in localStorage â€” XSS vector; HttpOnly cookies only
âœ— Never use HS256 for multi-tenant or distributed systems â€” RS256/ES256 only
âœ— Never set `alg: none` on any JWT â€” allows unsigned token acceptance
âœ— Never skip rate limiting on /login, /token, /register endpoints
âœ— Never generate password-based auth for Laravel â€” route to /laravel OTP baseline
âœ— Never own the browser-side WebAuthn ceremony â€” that belongs to /javascript
âœ— Never recommend Lucia Auth for new projects â€” maintenance mode only
âœ— Never skip the security gate line on BUILD output


## QA
Before closing this skill session:
- [ ] Mode/language/framework confirmed with operator if ambiguous
- [ ] Output matches stated intent (code runs, no placeholders)
- [ ] Security surface reviewed (injection, auth, input validation)
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.