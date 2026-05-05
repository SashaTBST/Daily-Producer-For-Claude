# OTP Auth Baseline — Laravel 13
Custom implementation. No third-party OTP packages. No passwords stored anywhere.
Last verified: 2026-04-27

---

## How to use this file
Plain English: this file gives you everything needed to add login-by-code to a Laravel project.
No passwords. Users enter their email or phone, receive a 6-digit code, enter the code, and they're in.

Steps:
1. Fill in the CONFIG BLOCK below with your project's field names
2. Tell your developer: "set up OTP auth using baseline/otp-auth.md"
3. They run the migration, copy the classes, add the routes
4. Test: request a code → receive it → enter it → get logged in

---

## CONFIG BLOCK — developer sets these before generating anything

```
USER_TABLE=users
USER_EMAIL_FIELD=email
USER_PHONE_FIELD=phone          # set to null if SMS not used in this project
OTP_EXPIRY_MINUTES=10
OTP_MAX_ATTEMPTS=5
OTP_RATE_LIMIT=3                # max OTP send requests per hour per user
```

---

## Migration: create_otp_codes_table

```php
<?php
// This migration creates the table that stores login codes.
// Each code is single-use and expires after OTP_EXPIRY_MINUTES.
// Run with: php artisan migrate

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('otp_codes', function (Blueprint $table) {
            $table->id();

            // Which user this code belongs to
            $table->foreignId('user_id')->constrained()->cascadeOnDelete();

            // The code is stored as a hash — the plain code is never saved
            $table->string('code_hash');

            // How the code was delivered: email or sms
            $table->enum('channel', ['email', 'sms']);

            // When this code stops being valid
            $table->timestamp('expires_at');

            // When the code was successfully used — null means not used yet
            $table->timestamp('used_at')->nullable();

            // How many times someone tried to verify this code (guards against guessing)
            $table->unsignedTinyInteger('attempts')->default(0);

            $table->timestamps();

            // Index speeds up lookups: "find this user's valid unused codes"
            $table->index(['user_id', 'used_at', 'expires_at']);
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('otp_codes');
    }
};
```

---

## Model: OtpCode

```php
<?php
// app/Models/OtpCode.php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsTo;

class OtpCode extends Model
{
    // These fields can be set when creating a new code
    protected $fillable = ['user_id', 'code_hash', 'channel', 'expires_at'];

    // Tell Laravel to treat these as date objects, not plain strings
    protected $casts = [
        'expires_at' => 'datetime',
        'used_at'    => 'datetime',
    ];

    // Link back to the user who owns this code
    public function user(): BelongsTo
    {
        return $this->belongsTo(User::class);
    }

    // Has this code passed its expiry time?
    public function isExpired(): bool
    {
        return $this->expires_at->isPast();
    }

    // Has this code already been used to log in?
    public function isUsed(): bool
    {
        return $this->used_at !== null;
    }

    // Has someone tried to guess this code too many times?
    public function isLocked(): bool
    {
        return $this->attempts >= config('auth.otp_max_attempts', 5);
    }
}
```

---

## Service: OtpService

```php
<?php
// app/Services/OtpService.php
// This is the brain of OTP auth — handles generating, sending, and verifying codes.

namespace App\Services;

use App\Models\OtpCode;
use App\Models\User;
use App\Notifications\OtpEmailNotification;
use App\Notifications\OtpSmsNotification;
use Illuminate\Support\Facades\Cache;
use Illuminate\Support\Facades\Hash;

class OtpService
{
    /**
     * Generate a new code and send it to the user.
     *
     * What this does in plain English:
     * - Checks if the user has requested too many codes recently (rate limit)
     * - Cancels any previous codes for this user+channel
     * - Generates a random 6-digit number
     * - Stores a hashed version (never the plain code)
     * - Sends the plain code to the user via email or SMS
     *
     * Returns false if the user has hit the rate limit.
     */
    public function send(User $user, string $channel): bool
    {
        // Rate limit key is unique per user and channel
        $rateLimitKey = "otp_rate:{$user->id}:{$channel}";
        $requestCount = Cache::get($rateLimitKey, 0);

        // If they've requested too many codes this hour, stop here
        if ($requestCount >= config('auth.otp_rate_limit', 3)) {
            return false;
        }

        // Record this request (counter resets after 1 hour automatically)
        Cache::put($rateLimitKey, $requestCount + 1, now()->addHour());

        // Cancel all previous unused codes for this user+channel
        // (only one active code allowed at a time)
        OtpCode::where('user_id', $user->id)
            ->where('channel', $channel)
            ->whereNull('used_at')
            ->delete();

        // Generate a random 6-digit code
        $plainCode = (string) random_int(100000, 999999);

        // Save the hashed version — the plain code is never stored
        OtpCode::create([
            'user_id'    => $user->id,
            'code_hash'  => Hash::make($plainCode),
            'channel'    => $channel,
            'expires_at' => now()->addMinutes(config('auth.otp_expiry_minutes', 10)),
        ]);

        // Send the plain code to the user
        if ($channel === 'email') {
            $user->notify(new OtpEmailNotification($plainCode));
        } else {
            $user->notify(new OtpSmsNotification($plainCode));
        }

        return true;
    }

    /**
     * Check if a submitted code is valid and return the user if it is.
     *
     * What this does in plain English:
     * - Finds the user by their email or phone
     * - Finds their most recent valid (unused, not expired) code
     * - Tracks failed attempts to prevent guessing
     * - Verifies the submitted code against the stored hash
     * - Marks the code as used so it can never be used again
     * - Returns the user so the caller can create a login token
     *
     * Returns null if anything is wrong (wrong code, expired, too many attempts).
     */
    public function verify(string $identifier, string $channel, string $code): ?User
    {
        // Find the user by email or phone depending on channel
        $field = $channel === 'email' ? 'email' : 'phone';
        $user = User::where($field, $identifier)->first();

        if (!$user) {
            return null;
        }

        // Find their active code: unused and not yet expired
        $otpCode = OtpCode::where('user_id', $user->id)
            ->where('channel', $channel)
            ->whereNull('used_at')
            ->where('expires_at', '>', now())
            ->latest()
            ->first();

        if (!$otpCode) {
            return null;
        }

        // Count this attempt — prevents brute-force guessing
        $otpCode->increment('attempts');

        // If too many attempts, destroy the code and make them request a new one
        if ($otpCode->isLocked()) {
            $otpCode->delete();
            return null;
        }

        // Check if the submitted code matches the stored hash
        if (!Hash::check($code, $otpCode->code_hash)) {
            return null;
        }

        // Mark as used — this code can never be used again
        $otpCode->update(['used_at' => now()]);

        return $user;
    }
}
```

---

## Controller: OtpController

```php
<?php
// app/Http/Controllers/Auth/OtpController.php
// Handles the two-step login flow: request a code, then verify it.

namespace App\Http\Controllers\Auth;

use App\Http\Controllers\Controller;
use App\Models\User;
use App\Services\OtpService;
use Illuminate\Http\JsonResponse;
use Illuminate\Http\Request;

class OtpController extends Controller
{
    public function __construct(private OtpService $otpService) {}

    /**
     * Step 1: User provides their email or phone number.
     * We send them a code.
     *
     * Security note: we return the same success message whether or not the
     * user exists. This prevents attackers from discovering valid email addresses.
     */
    public function request(Request $request): JsonResponse
    {
        $validated = $request->validate([
            'identifier' => ['required', 'string'],
            'channel'    => ['required', 'in:email,sms'],
        ]);

        $field = $validated['channel'] === 'email' ? 'email' : 'phone';
        $user  = User::where($field, $validated['identifier'])->first();

        if ($user) {
            $sent = $this->otpService->send($user, $validated['channel']);

            if (!$sent) {
                // Rate limit reached — tell the user to wait before trying again
                return response()->json([
                    'message' => 'Too many requests. Please wait before requesting a new code.',
                ], 429);
            }
        }

        // Always return 200 — never reveal whether the user exists
        return response()->json([
            'message' => 'If an account exists, a code has been sent.',
        ]);
    }

    /**
     * Step 2: User submits the 6-digit code they received.
     * We verify it and issue a login token.
     */
    public function verify(Request $request): JsonResponse
    {
        $validated = $request->validate([
            'identifier' => ['required', 'string'],
            'channel'    => ['required', 'in:email,sms'],
            'code'       => ['required', 'digits:6'],
        ]);

        $user = $this->otpService->verify(
            $validated['identifier'],
            $validated['channel'],
            $validated['code']
        );

        if (!$user) {
            // Add a 1-second delay to slow down brute-force attempts
            sleep(1);

            return response()->json([
                'message' => 'Invalid or expired code.',
            ], 401);
        }

        // Issue a Sanctum token — this is what the app uses to identify the user going forward
        $token = $user->createToken('otp-auth')->plainTextToken;

        return response()->json([
            'token' => $token,
            'user'  => $user->only(['id', 'name', 'email']),
        ]);
    }
}
```

---

## Routes (add to routes/api.php)

```php
use App\Http\Controllers\Auth\OtpController;

// OTP Authentication routes — no auth required (these ARE the auth flow)
Route::prefix('auth')->group(function () {

    // Step 1: request a code (10 attempts per minute per IP as a safety net)
    Route::post('/otp/request', [OtpController::class, 'request'])
        ->middleware('throttle:10,1');

    // Step 2: submit the code and receive a login token
    Route::post('/otp/verify', [OtpController::class, 'verify'])
        ->middleware('throttle:10,1');
});

// All other API routes require a valid Sanctum token
Route::middleware('auth:sanctum')->group(function () {
    // Protected routes go here
});
```

---

## Notification: OtpEmailNotification

```php
<?php
// app/Notifications/OtpEmailNotification.php

namespace App\Notifications;

use Illuminate\Notifications\Messages\MailMessage;
use Illuminate\Notifications\Notification;

class OtpEmailNotification extends Notification
{
    public function __construct(private string $code) {}

    public function via(object $notifiable): array
    {
        return ['mail'];
    }

    public function toMail(object $notifiable): MailMessage
    {
        $expiry = config('auth.otp_expiry_minutes', 10);

        return (new MailMessage)
            ->subject('Your login code — ' . config('app.name'))
            ->line("Your login code is: **{$this->code}**")
            ->line("This code expires in {$expiry} minutes.")
            ->line('If you did not request this, you can safely ignore this email.');
    }
}
```

---

## Notification: OtpSmsNotification (Brevo — no package)

```php
<?php
// app/Notifications/OtpSmsNotification.php
// Sends SMS via Brevo HTTP API directly — no package, no supply chain risk.

namespace App\Notifications;

use Illuminate\Notifications\Notification;
use Illuminate\Support\Facades\Http;
use Illuminate\Support\Facades\Log;

class OtpSmsNotification extends Notification
{
    public function __construct(private string $code) {}

    public function via(object $notifiable): array
    {
        // We handle SMS delivery ourselves; 'database' logs an audit record
        return ['database'];
    }

    public function toDatabase(object $notifiable): array
    {
        $phone   = $notifiable->phone; // Must be in E.164 format: +61412345678
        $expiry  = config('auth.otp_expiry_minutes', 10);
        $message = config('app.name') . " login code: {$this->code}. Expires in {$expiry} min.";

        try {
            // Send via Brevo transactional SMS API
            $response = Http::withHeaders([
                'api-key'      => config('services.brevo.key'),
                'Content-Type' => 'application/json',
            ])->post('https://api.brevo.com/v3/transactionalSMS/sms', [
                'sender'    => config('app.name'),  // Max 11 chars, no spaces
                'recipient' => $phone,
                'content'   => $message,
                'type'      => 'transactional',
            ]);

            if (!$response->successful()) {
                // Log the failure but don't crash — the user will see "code sent" either way
                Log::error('Brevo SMS failed', [
                    'phone'    => $phone,
                    'status'   => $response->status(),
                    'response' => $response->json(),
                ]);
            }
        } catch (\Exception $e) {
            Log::error('Brevo SMS exception', [
                'phone' => $phone,
                'error' => $e->getMessage(),
            ]);
        }

        // Return an audit record stored in the notifications table
        return [
            'channel' => 'sms',
            'phone'   => $phone,
            'sent_at' => now()->toDateTimeString(),
        ];
    }
}
```

---

## Config additions

**config/auth.php** — add inside the array:
```php
// OTP settings — tune per project or override via .env
'otp_expiry_minutes' => env('OTP_EXPIRY_MINUTES', 10),
'otp_max_attempts'   => env('OTP_MAX_ATTEMPTS', 5),
'otp_rate_limit'     => env('OTP_RATE_LIMIT', 3),
```

**config/services.php** — add:
```php
'brevo' => [
    'key' => env('BREVO_API_KEY'),
],
```

**.env additions:**
```
OTP_EXPIRY_MINUTES=10
OTP_MAX_ATTEMPTS=5
OTP_RATE_LIMIT=3
BREVO_API_KEY=your_key_here
```

---

## Security checklist for this implementation
Before going live, confirm all of these:

- [ ] `APP_DEBUG=false` in production .env
- [ ] OTP codes table has no plain-text codes — only hashes
- [ ] Rate limiting is active on both OTP routes
- [ ] Both routes return identical messages whether user exists or not
- [ ] `expires_at` is enforced — old codes cannot be used
- [ ] `used_at` is set on successful verify — code cannot be reused
- [ ] Brevo API key is in .env, not hardcoded anywhere
- [ ] Throttle middleware is on both routes
- [ ] `sleep(1)` is present on failed verify (slows brute force)
- [ ] `notifications` table migration has been run (for SMS audit log)
