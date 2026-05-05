# PHP Skill — Reference
PHP 8.4 | PHPUnit 11 | Composer | PHP-CS-Fixer | Last verified: 2026-04-27

## Research Sources
- PHP 8.4 release announcement: https://www.php.net/releases/8.4/en.php
- What's new in PHP 8.4 (stitcher.io): https://stitcher.io/blog/new-in-php-84
- PHP 8.4 full changelog (PHP.Watch): https://php.watch/versions/8.4
- PHP 8.3 vs 8.4 comparison: https://levelup.gitconnected.com/php-8-3-vs-php-8-4-the-definitive-comparison-for-developers-bce56e0f883d
- PHP migration 8.3 → 8.4 (official): https://www.php.net/manual/en/migration84.php
- PHP security best practices 2025: https://empowercodes.com/articles/php-security-best-practices-for-2025
- PHP vulnerabilities 2025 (TuxCare): https://tuxcare.com/blog/php-vulnerability/
- Secure PHP code — SQL/XSS/CSRF: https://metadesignsolutions.com/how-to-write-secure-php-code-preventing-sql-injection-xss-and-csrf/
- PHP 8.x features and use cases: https://www.innoraft.ai/blog/php-8-features-use-cases
- PHP features cheatsheet: https://eusonlito.github.io/php-changes-cheatsheet/features.html
- Composer PSR-4 autoloading: https://www.php-fig.org/psr/psr-4/
- PHPUnit 11 getting started: https://phpunit.de/getting-started/phpunit-11.html
- PHP-CS-Fixer vs PHP_CodeSniffer: https://thephp.cc/articles/phpcodesniffer-or-phpcsfixer
- OWASP Top 10 2025: https://owasp.org/Top10/2025/

---

## Version Compatibility

| Feature | 8.0 | 8.1 | 8.2 | 8.3 | 8.4 |
|---|---|---|---|---|---|
| Match expressions | ✓ | ✓ | ✓ | ✓ | ✓ |
| Named arguments | ✓ | ✓ | ✓ | ✓ | ✓ |
| Union types | ✓ | ✓ | ✓ | ✓ | ✓ |
| Nullsafe operator `?->` | ✓ | ✓ | ✓ | ✓ | ✓ |
| Enums | — | ✓ | ✓ | ✓ | ✓ |
| Readonly properties | — | ✓ | ✓ | ✓ | ✓ |
| Readonly classes | — | — | ✓ | ✓ | ✓ |
| Fibers | — | ✓ | ✓ | ✓ | ✓ |
| Typed class constants | — | — | — | ✓ | ✓ |
| Property hooks | — | — | — | — | ✓ |
| Asymmetric visibility | — | — | — | — | ✓ |
| array_find_key/all/any | — | — | — | — | ✓ |

---

## PHP 8.4 — Key Features

### Property Hooks (8.4 only)
```php
// PHP 8.4: computed property via get/set hooks
// Plain English: runs code automatically when the property is read or written
class User {
    public string $fullName {
        get => "$this->firstName $this->lastName";
    }

    public string $email {
        set(string $value) {
            $this->email = strtolower(trim($value)); // normalise on write
        }
    }
}
```

### Asymmetric Visibility (8.4 only)
```php
// PHP 8.4: public to read, private to write — no setter method needed
class Order {
    public private(set) string $status = 'pending';

    public function ship(): void {
        $this->status = 'shipped'; // only works inside the class
    }
}
// $order->status — readable from anywhere
// $order->status = 'x' — error: private(set)
```

### New Array Functions (8.4)
```php
$users = [['name' => 'Alice', 'active' => true], ['name' => 'Bob', 'active' => false]];

// Find first match
$active = array_find($users, fn($u) => $u['active']); // Alice's array

// Find key of first match
$key = array_find_key($users, fn($u) => !$u['active']); // 1

// Check if all/any match
$allActive = array_all($users, fn($u) => $u['active']); // false
$anyActive = array_any($users, fn($u) => $u['active']); // true
```

---

## Modern PHP Patterns (8.0+ — use by default)

### Match Expression
```php
// Strict comparison, returns a value, no fall-through, exhaustive by default
$status = match($code) {
    200 => 'OK',
    404 => 'Not Found',
    500 => 'Server Error',
    default => 'Unknown',
};
```

### Enums (8.1+)
```php
// Backed enum — carries a string value for DB/API storage
enum Status: string {
    case Active = 'active';
    case Inactive = 'inactive';
    case Pending = 'pending';
}

$status = Status::Active;
echo $status->value; // 'active'
$status = Status::from('pending'); // Status::Pending
```

### Readonly Properties (8.1+)
```php
// Set once in constructor, then immutable — perfect for DTOs
class ProductDto {
    public function __construct(
        public readonly string $name,
        public readonly int $priceInCents,
        public readonly string $sku,
    ) {}
}
```

### Nullsafe Operator
```php
// Replaces nested null checks — stops chain if null encountered
$city = $user?->getAddress()?->getCity()?->getName();
// Returns null instead of throwing if any step is null
```

---

## Security Patterns

### SQL Injection — PDO Prepared Statements
```php
// CORRECT — parameterised query, user input never touches SQL string
$stmt = $pdo->prepare('SELECT * FROM users WHERE email = :email AND active = 1');
$stmt->execute(['email' => $userInput]);
$user = $stmt->fetch(PDO::FETCH_ASSOC);

// WRONG — never do this
$result = $pdo->query("SELECT * FROM users WHERE email = '$userInput'");
```

### XSS Prevention — Output Escaping
```php
// Escape EVERYTHING from user input before outputting to HTML
echo htmlspecialchars($userInput, ENT_QUOTES | ENT_SUBSTITUTE, 'UTF-8');

// For JSON output (AJAX responses)
header('Content-Type: application/json');
echo json_encode($data, JSON_HEX_TAG | JSON_HEX_AMP | JSON_HEX_APOS | JSON_HEX_QUOT);
```

### CSRF Protection
```php
// Generate token on form render
$_SESSION['csrf_token'] = bin2hex(random_bytes(32));

// Verify on form submit
function validateCsrf(string $token): bool {
    return isset($_SESSION['csrf_token'])
        && hash_equals($_SESSION['csrf_token'], $token);
}
// Use hash_equals() — prevents timing attacks
```

### Session Security
```php
// Secure session configuration — set in php.ini or at session start
ini_set('session.cookie_httponly', '1');   // JS cannot access cookie
ini_set('session.cookie_secure', '1');     // HTTPS only
ini_set('session.use_strict_mode', '1');   // reject unrecognised session IDs

session_start();
session_regenerate_id(true); // regenerate after login — prevents fixation
```

### Password Hashing
```php
// Hash on registration/password change
$hash = password_hash($plainPassword, PASSWORD_ARGON2ID);

// Verify on login
if (password_verify($plainPassword, $storedHash)) {
    // authenticated
}

// Never use: md5(), sha1(), sha256() for passwords
```

### File Upload Validation
```php
function validateUpload(array $file, array $allowedMime = ['image/jpeg', 'image/png']): bool {
    // 1. Never trust $_FILES['type'] — user-controlled
    $finfo = new finfo(FILEINFO_MIME_TYPE);
    $detectedMime = $finfo->file($file['tmp_name']);

    // 2. Check against whitelist
    if (!in_array($detectedMime, $allowedMime, strict: true)) {
        return false;
    }

    // 3. Check extension against whitelist too
    $ext = strtolower(pathinfo($file['name'], PATHINFO_EXTENSION));
    if (!in_array($ext, ['jpg', 'jpeg', 'png'], strict: true)) {
        return false;
    }

    return true;
}
// Store uploaded files OUTSIDE the webroot — never in public/
```

---

## Composer Baseline

```json
{
  "name": "your-vendor/your-project",
  "require": {
    "php": "^8.4"
  },
  "require-dev": {
    "phpunit/phpunit": "^11.0",
    "friendsofphp/php-cs-fixer": "^3.0"
  },
  "autoload": {
    "psr-4": {
      "YourNamespace\\": "src/"
    }
  },
  "autoload-dev": {
    "psr-4": {
      "YourNamespace\\Tests\\": "tests/"
    }
  }
}
```

Commands:
```bash
composer install          # install from lock file (production)
composer update           # update dependencies (dev only — test before committing)
composer audit            # check for known security vulnerabilities — run before every deploy
composer dump-autoload -o # regenerate optimised autoloader
```

---

## PHPUnit 11 — Test Structure

```php
<?php
// tests/Unit/UserTest.php — mirrors src/User.php structure
namespace YourNamespace\Tests\Unit;

use PHPUnit\Framework\TestCase;
use PHPUnit\Framework\Attributes\Test;
use PHPUnit\Framework\Attributes\DataProvider;
use YourNamespace\User;

final class UserTest extends TestCase
{
    #[Test]
    public function it_formats_full_name(): void
    {
        $user = new User(firstName: 'Alice', lastName: 'Smith');
        $this->assertSame('Alice Smith', $user->fullName);
    }

    #[Test]
    #[DataProvider('invalidEmailProvider')]
    public function it_rejects_invalid_emails(string $email): void
    {
        $this->expectException(\InvalidArgumentException::class);
        new User(email: $email);
    }

    // Data providers must be public static
    public static function invalidEmailProvider(): array
    {
        return [
            'empty string' => [''],
            'no at sign'   => ['notanemail'],
            'no domain'    => ['user@'],
        ];
    }
}
```

Run: `./vendor/bin/phpunit`

---

## PHP-CS-Fixer Setup

```bash
# Install
composer require --dev friendsofphp/php-cs-fixer

# Create .php-cs-fixer.php in project root
```

```php
// .php-cs-fixer.php
<?php
$finder = PhpCsFixer\Finder::create()
    ->in(__DIR__ . '/src')
    ->in(__DIR__ . '/tests');

return (new PhpCsFixer\Config())
    ->setRules([
        '@PSR12' => true,
        'declare_strict_types' => true,
        'array_syntax' => ['syntax' => 'short'],
        'ordered_imports' => ['sort_algorithm' => 'alpha'],
        'no_unused_imports' => true,
    ])
    ->setFinder($finder);
```

Run: `./vendor/bin/php-cs-fixer fix`
Check only: `./vendor/bin/php-cs-fixer fix --dry-run --diff`

---

## CLI Script Pattern

```php
#!/usr/bin/env php
<?php
// How to run: php script.php --input=file.csv --output=results.json
// Exit codes: 0 = success, 1 = error, 2 = usage error

declare(strict_types=1);

// Parse options — getopt() returns false if parsing fails
$options = getopt('', ['input:', 'output:', 'help']);

if (isset($options['help'])) {
    echo "Usage: php script.php --input=<file> --output=<file>\n";
    exit(0);
}

if (!isset($options['input'], $options['output'])) {
    fwrite(STDERR, "Error: --input and --output are required.\n");
    exit(2); // usage error
}

$inputFile = $options['input'];

if (!file_exists($inputFile)) {
    fwrite(STDERR, "Error: input file not found: $inputFile\n");
    exit(1); // general error
}

// Main logic here
// ...

echo "Done.\n";
exit(0); // success
```

---

## OWASP 2025 REVIEW Checklist

| # | Check | What to look for | OWASP ref |
|---|---|---|---|
| 1 | SQL injection | All DB queries use PDO prepared statements; no string concatenation with user input | A03 |
| 2 | XSS output | All user-supplied output escaped with `htmlspecialchars(ENT_QUOTES)` | A03 |
| 3 | CSRF tokens | State-changing endpoints (POST/PUT/DELETE) verify CSRF token; `hash_equals()` used | A01 |
| 4 | Session security | `session_regenerate_id()` after login; httponly + secure cookies; strict mode | A07 |
| 5 | Password hashing | `password_hash()` with `PASSWORD_ARGON2ID`; no MD5/SHA1 for passwords | A07 |
| 6 | File upload | MIME validated with `finfo`, extension whitelisted, stored outside webroot | A04 |
| 7 | `eval()` absent | No `eval()` calls — remote code execution vector | A03 |
| 8 | Error handling | No raw error output to browser; exceptions caught and logged; `display_errors=Off` in production | A10 |
| 9 | Supply chain | `composer audit` run and clean; `composer.lock` committed | A06 |
| 10 | Access control | Every sensitive endpoint checks authentication and authorisation before processing | A01 |
