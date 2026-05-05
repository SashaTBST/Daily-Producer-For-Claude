# Laravel Skill — Reference
Laravel 13 / PHP 8.3 | Last verified: 2026-04-27

## Research Sources
Sources verified during build (2026-04-27):
- Laravel 13 release notes: https://laravel.com/docs/12.x/releases
- Laravel 13 features overview: https://laravel-news.com/laravel-13
- OTP packages surveyed: https://spatie.be/blog/a-package-to-handle-one-time-passwords-otp-in-laravel-apps | https://github.com/benbjurstrom/otpz
- OWASP Laravel cheat sheet: https://cheatsheetseries.owasp.org/cheatsheets/Laravel_Cheat_Sheet.html
- OWASP Top 10 2025 for Laravel: https://medium.com/@ilyaskazi/fortify-your-laravel-app-the-owasp-top-10-2025-edition-b3bf7ec3bfec
- Laravel security best practices 2026: https://benjamincrozat.com/laravel-security-best-practices
- AWS EC2 Laravel deployment: https://laraveldaily.com/course/deploy-laravel-aws-ec2
- Inertia.js docs: https://inertiajs.com/
- Xero OAuth2 package: https://github.com/webfox/laravel-xero-oauth2
- Prior art — official Laravel skills: https://github.com/laravel/claude-code | https://github.com/laravel/agent-skills
- Prior art — Laravel API skill: https://github.com/JustSteveKing/laravel-api-skill

---

## Baseline Package Stack
Install on every new project in this order.

**Core**
- `laravel/sanctum` — API token auth + SPA auth (included in L13, configure explicitly)
- `laravel/horizon` — Queue management UI (requires Redis/ElastiCache — already provisioned)
- `laravel/telescope` — Debug assistant (dev environment only — block in production via env gate)
- `spatie/laravel-permission` — Role and permission management
- `spatie/laravel-activitylog` — Audit trail for all model changes

**Dev tooling**
- `barryvdh/laravel-debugbar` — Dev only
- `nunomaduro/larastan` — Static analysis (PHPStan for Laravel) — run before every PR

**Do not install** without verifying: author reputation, star count, last commit date, and that the
Packagist listing matches the GitHub repo. Malicious packages were found on Packagist March 2026.

---

## OWASP 2025 Security Checklist (Laravel)
Run on every REVIEW. Report each item as PASS / WARN / FAIL.

| # | Check | Laravel implementation |
|---|---|---|
| 1 | Broken Access Control | Gates + Policies on every resource. No unguarded route unless documented. |
| 2 | Security Misconfiguration | APP_DEBUG=false in prod. .env not in git. Only /public in webroot. |
| 3 | Supply Chain | Every package verified before install. composer.lock committed. |
| 4 | Injection | Eloquent/Query Builder only. No raw SQL with user input. All inputs validated. |
| 5 | Auth failures | OTP-only auth (no passwords). Token expiry enforced. OTP rate limited. |
| 6 | Sensitive data | No PII in logs. Encrypted fields where needed. HTTPS enforced. Secure cookies. |
| 7 | XSS | Blade {{ }} escapes by default. {!! !!} only for trusted content, commented why. |
| 8 | CSRF | VerifyCsrfToken middleware active on all non-API routes. |
| 9 | Insecure design | No password reset flow (OTP replaces it). No security through obscurity. |
| 10 | Error handling | Custom error pages. No stack traces in production. Logs to file or CloudWatch. |

---

## AWS EC2 Deployment Pattern
Stack: EC2 (Nginx + PHP-FPM 8.3) + RDS MySQL 8 + ElastiCache Redis + SQS + S3 + CloudFront

**Security groups — configure before anything else**
- SSH (22): operator IP only — never 0.0.0.0/0
- HTTP (80): 0.0.0.0/0 → redirect to HTTPS immediately
- HTTPS (443): 0.0.0.0/0
- MySQL (3306): EC2 security group only — never publicly accessible
- Redis (6379): EC2 security group only — never publicly accessible

**Server setup order (DEPLOY mode runs these steps with explanations)**
1. EC2 launch → Ubuntu 24.04 LTS → t3.medium minimum for production
2. Install: Nginx, PHP 8.3-FPM, Composer, Node (for asset compilation)
3. Clone repo → `composer install --no-dev --optimize-autoloader` → `npm run build`
4. `.env` — pull from AWS Secrets Manager or SSM Parameter Store (never type secrets manually)
5. `php artisan migrate --force` → `php artisan config:cache` → `php artisan route:cache`
6. Nginx config → document root points to `/public` → SSL via Certbot or ACM
7. Supervisor → manages Laravel queue workers (Horizon)
8. `php artisan storage:link` → configure S3 filesystem disk
9. CloudFront → CDN in front of EC2 ALB

**Hard rules**
- Never use `php artisan serve` in production
- Never leave Telescope active in production (env gate: `APP_ENV !== production`)
- Always set APP_DEBUG=false before the first public request
- Always run `php artisan config:clear` after any .env change

---

## Integration Registry
Last verified: 2026-04-27

### Xero
Package: `webfox/laravel-xero-oauth2` — maintained, supports L12/L13, OAuth 2.0
Pattern: OAuth 2.0 token flow → store encrypted token in DB → refresh on expiry via middleware
Docs: https://github.com/webfox/laravel-xero-oauth2
Config: add `XERO_CLIENT_ID`, `XERO_CLIENT_SECRET`, `XERO_REDIRECT_URI` to .env

### Lark / Feishu (ByteDance workplace platform)
Package: None — use Laravel HTTP facade directly (no supply chain exposure)
Auth: App Access Token endpoint — `POST https://open.larksuite.com/open-apis/auth/v3/app_access_token/internal`
API calls:
```php
Http::withToken($accessToken)
    ->post('https://open.larksuite.com/open-apis/[endpoint]', $payload);
```
Webhooks: verify `X-Lark-Signature` header before processing any inbound event
Note: confirm base URL for your region — global (`larksuite.com`) vs China (`feishu.cn`)
Config: add `LARK_APP_ID`, `LARK_APP_SECRET` to .env

### Vue 3 + Inertia.js (web UI layer)
Pattern: modern monolith — Laravel handles routing + data, Inertia bridges to Vue 3 components
Install: `composer require inertiajs/inertia-laravel` + `npm install @inertiajs/vue3`
Auth: Sanctum SPA auth for the web UI (cookie-based session)
API layer: Sanctum token auth for mobile apps, external integrations, webhooks
Rule: use Inertia for web UI. Use Sanctum API tokens for everything else.

### Claude AI SDK (Laravel 13 native)
Laravel 13 ships a stable, provider-agnostic AI SDK — Claude is a first-class provider.
```php
use Illuminate\Support\Facades\AI;
$response = AI::text('Your prompt here'); // returns string
```
Config: `config/ai.php` → set provider to `anthropic`
.env: add `ANTHROPIC_API_KEY`
For streaming, tool use, or structured output — see Laravel AI SDK docs.

### Brevo SMS (OTP delivery)
Package: None — HTTP facade only (no supply chain exposure on a security-critical path)
```php
Http::withHeaders(['api-key' => config('services.brevo.key'), 'Content-Type' => 'application/json'])
    ->post('https://api.brevo.com/v3/transactionalSMS/sms', [
        'sender'    => config('app.name'),
        'recipient' => $phone, // E.164 format: +61412345678
        'content'   => "Your code: {$code}. Expires in 10 minutes.",
        'type'      => 'transactional',
    ]);
```
Config: add `BREVO_API_KEY` to .env, add to `config/services.php`
Fallback: Twilio — `composer require twilio/sdk` (official SDK, acceptable supply chain risk)

---

## Adding New Integrations
1. Check if an official SDK exists — if yes, verify it before installing
2. If no SDK or SDK is low-quality: use Laravel HTTP facade instead
3. Store all credentials in .env → access via `config('services.[name].[key]')`
4. Create a dedicated Service class: `app/Services/[Name]Service.php`
5. Add an entry to this registry with the Last verified date
6. Write a Feature test covering the integration's primary action
