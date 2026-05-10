# security-test — REFERENCE

## Tool Install (Windows)

```powershell
# nuclei
go install -v github.com/projectdiscovery/nuclei/v3/cmd/nuclei@latest
nuclei -update-templates

# nikto (requires Perl — use WSL or prebuilt binary)
# https://github.com/sullo/nikto/releases

# gobuster
go install github.com/OJ/gobuster/v3@latest

# ffuf
go install github.com/ffuf/ffuf/v2@latest

# sqlmap (Python)
pip install sqlmap
# or: git clone https://github.com/sqlmapproject/sqlmap

# whatweb
gem install whatweb
# or use WSL: sudo apt install whatweb

# interact.sh (OOB DNS callbacks)
# https://github.com/projectdiscovery/interactsh
go install -v github.com/projectdiscovery/interactsh/cmd/interactsh-client@latest
```

---

## CLI Commands — Full Reference

### 1. Fingerprint (whatweb)
```powershell
whatweb [target] --log-json=whatweb-[nonce].json
```

### 2. Server scan (nikto)
```powershell
# Basic
nikto -h [target] -o nikto-[nonce].json -Format json

# With custom User-Agent (calling card)
nikto -h [target] -useragent "Claude-PenTest-[nonce]-[YYYY-MM-DD]" -o nikto-[nonce].json -Format json
```

### 3. Nuclei sweep (scan mode)
```powershell
# Standard sweep with calling card UA
nuclei -u [target] `
  -tags xss,sqli,lfi,rce,auth,misconfig,headers,cve `
  -H "X-PenTest-ID: [nonce]" `
  -H "User-Agent: Claude-PenTest-[nonce]-[YYYY-MM-DD]" `
  -o nuclei-[nonce].json -json `
  -rate-limit 25 `
  -timeout 10

# OWASP ASVS compliance scan
nuclei -u [target] -tags asvs -rate-limit 25 -json -o nuclei-asvs-[nonce].json
```

### 4. Directory fuzzing (gobuster)
```powershell
gobuster dir `
  -u [target] `
  -w $env:USERPROFILE\wordlists\common.txt `
  -o gobuster-[nonce].txt `
  --delay 100ms `
  --useragent "Claude-PenTest-[nonce]-[YYYY-MM-DD]" `
  --add-slash
```

### 5. Parameter fuzzing (ffuf)
```powershell
ffuf `
  -u "[target]?FUZZ=test" `
  -w $env:USERPROFILE\wordlists\params.txt `
  -o ffuf-[nonce].json -of json `
  -p 0.1 `
  -H "User-Agent: Claude-PenTest-[nonce]-[YYYY-MM-DD]"
```

### 6. SQL injection (sqlmap — inject mode only)
```powershell
# Requires second explicit operator gate before running
# Static site check: if no forms or query params detected — skip entirely

sqlmap `
  -u "[target]?id=1" `
  --batch `
  --level 2 `
  --risk 1 `
  --agent "Claude-PenTest-[nonce]-[YYYY-MM-DD]" `
  --output-dir=sqlmap-[nonce]
```

### 7. DNS OOB callback (Tier 1B — CDN targets)
```powershell
# Start interact.sh client first — get your OOB host
interactsh-client -v

# Inject OOB host into nuclei scan
nuclei -u [target] -iserver [your-oob-host] -rate-limit 10 -json -o nuclei-oob-[nonce].json
```

---

## Nonce Generation (Windows)

```powershell
$nonce = (New-Guid).ToString("N").Substring(0,8)
Write-Output $nonce
```

---

## Calling Card File Template (Tier 2)

```
PENTEST CALLING CARD
Target: [full URL]
Path: [path where this file was dropped]
Nonce: [nonce]
Date: [YYYY-MM-DD HH:MM UTC]
Tester: [operator name / email]
Scope ref: [authorization document or session date]
Finding: [what surface allowed this write]
Action: run /security-review — patch write surface and remove this file
```

---

## Wordlists (Windows paths)

```
$env:USERPROFILE\wordlists\common.txt        — SecLists Discovery/Web-Content/common.txt
$env:USERPROFILE\wordlists\params.txt        — SecLists Discovery/Web-Content/burp-parameter-names.txt
$env:USERPROFILE\wordlists\directories.txt   — SecLists Discovery/Web-Content/raft-medium-directories.txt
```

Download SecLists: `git clone https://github.com/danielmiessler/SecLists ~/wordlists-src`

---

## ZAP Extended Mode (optional — deep crawl)

Use when: full spider + active scan needed beyond nuclei scope.

```powershell
# Start ZAP daemon (requires Java)
& "C:\Program Files\OWASP\Zed Attack Proxy\zap.bat" -daemon -host 127.0.0.1 -port 8080 -config api.disablekey=true

# Run spider via REST API
curl http://127.0.0.1:8080/JSON/spider/action/scan/?url=[target]

# Run active scan
curl http://127.0.0.1:8080/JSON/ascan/action/scan/?url=[target]

# Get results
curl http://127.0.0.1:8080/JSON/alert/view/alerts/?baseurl=[target] > zap-[nonce].json
```

---

## Output File Structure

`_AI/MEMORY/qa-reports/pentest-[target-slug]-[YYYY-MM-DD]-Staging.md`

```markdown
# Pentest Report — [target] — [YYYY-MM-DD]

Nonce: [nonce]
Mode: [mode run]
Calling card: [tier landed — log marker / OOB callback / file drop at path]
Tools run: [list]

## Findings

[PENTEST-FINDING blocks]

## PENTEST SUMMARY
[N critical] [N high] [N medium] [N low] [N info]
Top priority: [most critical item]
Static site: [yes/no — inject skipped / full surface available]
Next: run /security-review [this file path]
```

---

## Static Site Detection Checklist

whatweb output signals static site when:
- No CMS detected (no WordPress, Drupal, etc.)
- Framework = 11ty / Jekyll / Hugo / Next.js (static export)
- No `/wp-admin`, `/admin`, `/login` paths found
- No form POST actions with dynamic endpoints
- Server = Nginx/Apache serving pre-built HTML only

If static: skip inject mode. Note in report. Do not flag as "injection-clean."

---

## Scope — Out-of-Scope Handling

When gobuster/ffuf discovers a redirect to external domain:
```
[OUT-OF-SCOPE DETECTED]
URL: [external URL]
Found via: [tool + path]
Action: flagged only — not tested
```
Never follow or test external domains without separate authorization confirmation.