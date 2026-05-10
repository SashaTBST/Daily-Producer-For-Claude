---
name: security-test
description: Active web penetration tester — runs CLI tools (nuclei, nikto, gobuster, sqlmap) against operator-owned targets, plants calling cards as proof of access, and writes structured findings for /security-review to action. Use after a site goes live or before running /security-review to generate findings.
argument-hint: "[target URL] [mode: recon|scan|fuzz|inject|full|card]"
---

## Authorization Gate — Mandatory

Before any execution, output:
```
AUTHORIZATION CHECK
Target: [URL]
Confirm you own or have written authorization to test this target. (yes/no)
```
If no: stop. Never proceed. No exceptions.

## Pre-Flight

Run before any tool:
1. Nuclei templates installed? → `nuclei -update-templates` if missing
2. CDN/proxy detected? (from whatweb) → enable Tier 1B DNS OOB calling card
3. Static site detected? → auto-skip Tier 2 file drop + inject mode
4. Scope gate: list in-scope URLs — external assets flagged only, never scanned
5. **Hosting detection → set rate limit + whitelist:**
   - cPanel / LiteSpeed / shared hosting → `-rate-limit 5` AND whitelist testing IP in CSF before scanning
   - Dedicated / VPS / cloud → `-rate-limit 25`
   - Unknown → `-rate-limit 5` (safe default)
   - Detect: check `Server:` response header — LiteSpeed = cPanel tier
   - cPanel CSF whitelist: cPanel → CSF Firewall → Quick Unblock / csf.allow → add tester IP
   - Get tester IP: `(Invoke-WebRequest -Uri "https://api.ipify.org" -UseBasicParsing).Content`

## Modes

`recon`  — whatweb fingerprint → nikto server scan
`scan`   — nuclei full sweep (xss, sqli, lfi, rce, auth, misconfig, headers)
`fuzz`   — gobuster dir + ffuf param fuzzing (rate-limited by default)
`inject` — sqlmap (second explicit gate — static sites auto-skipped)
`full`   — recon → scan → fuzz → inject in order, each phase gated
`card`   — calling card drop only, no scan

## Calling Card Protocol

**Tier 1 — Log marker (always on every request):**
User-Agent: `Claude-PenTest-[nonce]-[YYYY-MM-DD]`
Header: `X-PenTest-ID: [nonce]`
Nonce (Windows): `(New-Guid).ToString("N").Substring(0,8)`

**Tier 1B — DNS OOB (CDN/proxy targets):**
Nuclei interact.sh ping — proves request reached network layer regardless of edge caching.

**Tier 2 — File drop (writable surface only):**
Write `pentest-proof-[nonce]-[YYYY-MM-DD].txt` to discovered writable path.
Contents: target URL, nonce, tester ID, timestamp, scope ref.
Static sites: auto-skipped — state "no writable surface detected, Tier 1 only."

## Run Sequence (full mode)

1. `whatweb [target]` — stack fingerprint, static/dynamic detection, CDN presence
2. `nikto -h [target] -o nikto-[nonce].json -Format json`
3. `nuclei -u [target] -tags xss,sqli,lfi,rce,auth,misconfig,headers -o nuclei-[nonce].json -json -rate-limit [5|25]`
4. `gobuster dir -u [target] -w common.txt -o gobuster-[nonce].txt --delay 100ms`
5. `ffuf -u [target]/FUZZ -w params.txt -o ffuf-[nonce].json -p 0.1`
6. sqlmap — inject gate fires here (second confirmation required)

Full CLI flags and Windows variants: REFERENCE.md

## Preview Mode

Before each phase: output the exact commands that will run. Wait for operator confirmation before executing.

## Output Format

Each finding:
```
[PENTEST-FINDING]
Tool: [tool]
Severity: CRITICAL|HIGH|MEDIUM|LOW|INFO
Location: [url/path]
Evidence: [confirmed finding]
Calling card: [tier used — how access was proven]
Fix: [action for /security-review]
```

Write all findings to: `_AI/MEMORY/qa-reports/pentest-[target-slug]-[YYYY-MM-DD]-Staging.md`
End every run: `PENTEST COMPLETE — [N findings] — run /security-review [path] to action`

## Anti-patterns

✗ Never execute before authorization confirmation — no exceptions
✗ Never report "injection-clean" on a static site — state "no dynamic surface, inject skipped"
✗ Never scan out-of-scope URLs discovered during fuzz — flag only, never test
✗ Never run fuzz tools without rate limiting
✗ Never skip calling card — even zero-finding runs must prove the test reached the server

## QA

- [ ] Authorization confirmed before any execution
- [ ] Pre-flight complete (templates, CDN, static detection, scope list)
- [ ] Calling card landed and verified (log marker or OOB callback confirmed)
- [ ] All findings written to staging file at correct path
- [ ] PENTEST COMPLETE block output at end of run
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.
