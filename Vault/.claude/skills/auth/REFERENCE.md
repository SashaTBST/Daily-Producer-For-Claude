# /auth — Reference

## OAuth2/OIDC Build Guide

**Pattern:** Authorization Code Flow + PKCE (RFC 9700, Jan 2025). No exceptions.

```
Flow:
1. App generates code_verifier (random 43–128 char string)
2. App hashes it → code_challenge (S256)
3. Redirect user to provider: /authorize?response_type=code&code_challenge=...&code_challenge_method=S256
4. Provider returns authorization_code to redirect_uri
5. App POSTs code + code_verifier to /token endpoint (never expose in browser)
6. Receive access_token + id_token (OIDC) + refresh_token
7. Store tokens in HttpOnly cookies — never in JS-accessible storage
```

**Libraries (Node/Bun):**
- `better-auth` — full-stack, built-in OAuth2/OIDC, database sessions, framework-agnostic
- `openid-client` — low-level OIDC/OAuth2 protocol client (RFC-compliant, use when full control needed)
- `passport.js` — legacy; use only when existing Passport codebase

**Enforce:** exact redirect_uri matching · state parameter (CSRF) · nonce (OIDC replay) · short-lived access tokens

---

## JWT Build Guide

**Algorithms:**
- RS256 / ES256 — asymmetric; use for multi-tenant, distributed, or any system where multiple services validate tokens
- HS256 — symmetric; only for single-service, single-key systems where you fully control all consumers
- Never HS256 across services — key sharing = compromise blast radius

**Token storage:** HttpOnly + Secure + SameSite=Lax cookies only.
SameSite=Strict if no cross-site form submissions needed.

**Expiry pattern:**
- Access token: 5–15 minutes
- Refresh token: 7–30 days, rotate on every use, invalidate old token on rotation
- Store refresh tokens hashed server-side (DB or Redis)

**Library:** `jose` (TypeScript-first, FIDO-compliant, replaces `jsonwebtoken`)

```typescript
import { SignJWT, jwtVerify, importPKCS8, importSPKI } from 'jose'

// Sign
const token = await new SignJWT({ sub: userId, role })
  .setProtectedHeader({ alg: 'RS256' })
  .setIssuedAt()
  .setExpirationTime('15m')
  .sign(privateKey)

// Verify — always validate iss, aud, exp
const { payload } = await jwtVerify(token, publicKey, {
  issuer: 'https://yourapp.com',
  audience: 'api',
})
```

**JWK endpoint:** expose `/.well-known/jwks.json` for public key distribution in distributed systems.

---

## WebAuthn / Passkeys Build Guide (Server Side Only)

Browser-side ceremony (`navigator.credentials.create` / `navigator.credentials.get`) → `/javascript`.
/auth handles: challenge generation, credential verification, credential storage.

**Library:** `@simplewebauthn/server` (FIDO Conformance-tested, TypeScript-first)

```
Registration ceremony (server):
1. generateRegistrationOptions() → send challenge + options to client
2. Client performs browser ceremony → sends response
3. verifyRegistrationResponse() → verify + extract credential
4. Store: credentialID, credentialPublicKey, counter, user.id

Authentication ceremony (server):
1. generateAuthenticationOptions() → send challenge to client
2. Client performs browser ceremony → sends response
3. verifyAuthenticationResponse() → verify signature + increment counter
4. Counter mismatch → credential cloned, reject + alert
```

**Storage requirements:** credential public key (base64url), credential ID, sign counter, user handle, transports list.

---

## Session Management Build Guide

**When to use:** monolithic web app, server-rendered pages, when immediate revocation is required.
**Not for:** distributed systems, microservices, mobile API clients → use JWT instead.

**Library options:**
- `iron-session` — encrypted, stateless cookie sessions (Node/Bun, framework-agnostic, no server storage needed)
- `express-session` — server-side session store; requires Redis/DB adapter for production

**Cookie config (non-negotiable):**
```typescript
{
  httpOnly: true,
  secure: process.env.NODE_ENV === 'production',
  sameSite: 'lax',
  maxAge: 60 * 60 * 24 * 7, // 7 days
  path: '/',
}
```

**Session fixation prevention:** regenerate session ID on every privilege change (login, role change, password reset).

---

## Library Recommendations Table

| Use case | Recommended | Alternative | Avoid |
|---|---|---|---|
| Full auth system (Node/Bun) | `better-auth` (v1.x, 2025) | `auth.js` (Next.js-primary) | Lucia (maintenance mode) |
| JWT issuance + validation | `jose` | — | `jsonwebtoken` (legacy) |
| OAuth2/OIDC protocol client | `openid-client` | `better-auth` built-in | `passport-oauth2` |
| Passkeys / WebAuthn | `@simplewebauthn/server` | — | Roll your own |
| Cookie sessions (stateless) | `iron-session` | `express-session` + Redis | Plaintext cookies |
| Auth utilities (PKCE, etc.) | `oslo` | — | — |

Last verified: 2026-05-02. Re-verify before recommending in new projects.

---

## Security / REVIEW Checklist

**Token storage**
- [ ] No tokens in localStorage or sessionStorage
- [ ] HttpOnly + Secure + SameSite set on all auth cookies
- [ ] Refresh tokens stored hashed server-side (not raw)

**OAuth2 / OIDC**
- [ ] Auth Code + PKCE only — no Implicit Flow
- [ ] state parameter validated to prevent CSRF
- [ ] nonce validated (OIDC) to prevent replay
- [ ] redirect_uri exact match enforced on server
- [ ] access_token never logged or exposed in URL

**JWT**
- [ ] alg validated — `alg: none` rejected
- [ ] RS256 or ES256 used for multi-tenant / distributed
- [ ] iss, aud, exp validated on every verify call
- [ ] Refresh token rotated on every use — old token invalidated
- [ ] Access token expiry ≤ 15 minutes

**WebAuthn**
- [ ] Challenge is random, single-use, server-generated
- [ ] Sign counter checked — mismatch = cloned credential
- [ ] Origin and rpId validated on every ceremony
- [ ] Credential stored with full transports list

**Session**
- [ ] Session ID regenerated on login and privilege change
- [ ] CSRF token present for state-changing requests (if SameSite=None)
- [ ] Session expiry enforced server-side, not only via cookie maxAge
- [ ] Session store uses Redis or equivalent in production (not in-memory)

**General**
- [ ] Rate limiting on /login, /token, /register, /reset-password
- [ ] Account enumeration prevention (same response for unknown email vs wrong password)
- [ ] Auth endpoints exclude from general request logging (no credentials in logs)
- [ ] Security headers: HSTS, X-Frame-Options, X-Content-Type-Options present

---

## Laravel Conflict Rule

Laravel operators → route to `/laravel`. Load `baseline/otp-auth.md`.
/auth never generates password-based auth, session auth, or Sanctum/Fortify scaffolding for Laravel.
Reason: /laravel has an OTP-only baseline ("never store passwords") — /auth output would conflict.
If operator explicitly asks for Sanctum or Fortify: acknowledge the pattern, then hand off to `/laravel`.

---

## Planned Gap Skills

These auth areas have no dedicated skill yet. Flag when triggered and redirect:

| Type | Redirect |
|---|---|
| TOTP / SMS two-factor auth (2FA) | No skill yet — flag |
| Magic link / email OTP (non-Laravel) | `better-auth` magic link plugin in BUILD |
| SSO / SAML enterprise auth | No skill yet — flag |
| API key management (rotation, scoping) | `/api-design` for design, `/node` for implementation |
