---
name: auth
description: Security-first authentication skill covering OAuth2/OIDC, JWT, passkeys/WebAuthn, and session management. Guides pattern selection, builds implementations, and audits existing auth. Use when adding any form of user authentication to a web app, API, or game server.
argument-hint: "[mode: guide|build|review] [pattern: oauth2|jwt|webauthn|session] [description] — add 'raw' for clean code without narration"
---

## Audience
Default: NON-DEV MODE — every output includes plain-English explanation above code. Override: include `raw` for clean code without narration.

## Scope
/auth = auth patterns, token issuance, session management, auth module code.
Node/Bun is the primary code output. Other stacks: /auth establishes the pattern in plain English, then routes to the language skill for implementation.
WebAuthn = server side only (SimpleWebAuthn server package). Browser-side ceremony → `/javascript`.
Laravel: /auth routes to `/laravel` — load `baseline/otp-auth.md`. Never generates password-based Laravel auth.
Route/middleware wiring → `/node`. Auth requirements in API spec → `/api-design`.

## Modes

**GUIDE** — No pattern specified, or operator unsure what they need.
Ask two questions (in order — stop after first if answer is decisive):
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

**BUILD** — Pattern is named. Generate the implementation.
Accepted patterns: `oauth2` · `jwt` · `webauthn` · `session`
Full build guides and library table → REFERENCE.md.
Always Node/Bun primary. Fastify default; Express on request. Output all files in one response.
End with: propose `/node` to wire the auth module into server routes.

**REVIEW** — Audit existing auth code or config.
Full checklist → REFERENCE.md. Report each item: PASS / WARN / FAIL.
End with: propose `/qa` when clean.

## Security Gate
Every BUILD response ends with this line — never skip it:
`Security pass: ✓ no localStorage token storage ✓ PKCE enforced on OAuth2 ✓ RS256/ES256 not HS256 for multi-tenant ✓ HttpOnly + Secure + SameSite on cookies ✓ rate limiting on auth endpoints documented ✓ no alg:none`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration
- `/node` — wire auth module into routes and middleware (/auth stops at auth module boundary)
- `/javascript` — browser-side WebAuthn ceremony (`navigator.credentials`)
- `/api-design` — define auth requirements before /auth builds them
- `/laravel` — Laravel auth; load `baseline/otp-auth.md`
- `/backend` — routes here when auth is the task layer
- `/tdd` — propose after BUILD to cover token issuance, validation, and expiry paths

## Anti-patterns
✗ Never implement Implicit Flow — deprecated; Auth Code + PKCE only
✗ Never store tokens in localStorage — XSS vector; HttpOnly cookies only
✗ Never use HS256 for multi-tenant or distributed systems — RS256/ES256 only
✗ Never set `alg: none` on any JWT — allows unsigned token acceptance
✗ Never skip rate limiting on /login, /token, /register endpoints
✗ Never generate password-based auth for Laravel — route to /laravel OTP baseline
✗ Never own the browser-side WebAuthn ceremony — that belongs to /javascript
✗ Never recommend Lucia Auth for new projects — maintenance mode only
✗ Never skip the security gate line on BUILD output
