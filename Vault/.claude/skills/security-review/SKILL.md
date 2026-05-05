---
name: security-review
description: Complete a security review of code, configs, or infrastructure — covers OWASP Top 10, injection, auth flaws, secrets exposure, dependency risks, and security misconfigurations. Use when auditing code before shipping, reviewing PRs for security, or doing a full project security pass.
argument-hint: "[paste code/config, or path to review] [scope: full|quick|deps|auth|injection]"
---

## Modes

`full` — complete OWASP Top 10 pass + dependency audit + secrets scan
`quick` — high-priority issues only: injection, auth, secrets exposure
`deps` — dependency vulnerability scan: known CVEs, unpinned versions, supply chain risks
`auth` — authentication and authorisation audit: JWT, session, RBAC, privilege escalation
`injection` — injection surface audit: SQL, command, XSS, SSTI, path traversal

## Review Checklist (OWASP Top 10 — 2021)

1. **Broken Access Control** — IDOR, missing auth checks, path traversal, CORS misconfiguration
2. **Cryptographic Failures** — hardcoded secrets, weak algorithms, unencrypted data at rest/transit
3. **Injection** — SQL, NoSQL, command, LDAP, XSS, SSTI — parameterised queries always
4. **Insecure Design** — missing rate limiting, no abuse-case modelling, trust boundary violations
5. **Security Misconfiguration** — default creds, open ports, verbose errors, missing security headers
6. **Vulnerable Components** — outdated deps, known CVEs, unmaintained packages
7. **Auth & Session Failures** — weak passwords, no MFA, insecure session tokens, JWT `alg:none`
8. **Integrity Failures** — unsigned packages, no CI/CD integrity checks, insecure deserialisation
9. **Logging Failures** — no audit trail, logging sensitive data, missing alerting
10. **SSRF** — unvalidated redirects, internal network requests, metadata endpoint exposure

## Output Format

Each finding:
```
[SEVERITY: CRITICAL|HIGH|MEDIUM|LOW|INFO]
Location: [file:line or component]
Issue: [what is wrong]
Risk: [what an attacker can do]
Fix: [specific remediation]
```

End every review with:
```
SECURITY SUMMARY: [N critical] [N high] [N medium] [N low]
Top priority fix: [most critical item]
```

## Non-Negotiable Rules

- Never mark a finding INFO when it is genuinely exploitable — call it what it is
- Always check for secrets in git history, not just current files
- Dependency audit includes transitive deps, not just direct
- Auth review must include token expiry, rotation, and revocation paths

## Anti-patterns
✗ Never skip the OWASP checklist for "small" codebases — size does not reduce attack surface
✗ Never report "no issues found" without running all applicable checks
✗ Never suggest security-through-obscurity as a mitigation
✗ Never ignore low/info findings — attackers chain them
✗ Never audit only the code provided — ask about auth layer, infra, and deployment if not shown

## QA
Before closing:
- [ ] All applicable OWASP categories checked
- [ ] Every finding includes severity, location, risk, and fix
- [ ] SECURITY SUMMARY block written
- [ ] Dep audit run if code includes package files
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.
