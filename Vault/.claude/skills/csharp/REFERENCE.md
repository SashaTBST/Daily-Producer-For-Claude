# C# Skill — Reference
C# 14 / .NET 10 | #nullable enable | Last verified: 2026-04-30

## Research Sources
- What's new in C# 14: https://learn.microsoft.com/en-us/dotnet/csharp/whats-new/csharp-14
- What's new in C# 13: https://learn.microsoft.com/en-us/dotnet/csharp/whats-new/csharp-13
- Nullable reference types: https://learn.microsoft.com/en-us/dotnet/csharp/nullable-references
- Records: https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/record
- OWASP .NET Security Cheat Sheet: https://cheatsheetseries.owasp.org/cheatsheets/DotNet_Security_Cheat_Sheet.html
- xUnit vs NUnit vs MSTest 2026: https://codingdroplets.com/xunit-vs-nunit-vs-mstest-in-net-which-testing-framework-should-your-team-use-in-2026
- Async antipatterns: https://markheath.net/post/async-antipatterns
- Dispose pattern: https://blog.ivankahl.com/csharp-dispose-pattern/

---

## C# 14 — Key Features (use by default)

### Primary Constructors
```csharp
// Replaces boilerplate constructor + field declarations
public class UserService(IUserRepository repo, ILogger<UserService> logger)
{
    public async Task<User?> GetByIdAsync(int id) =>
        await repo.GetByIdAsync(id);
}
```

### Records (immutable data)
```csharp
// Use for DTOs, value objects, API request/response models
public record CreateUserRequest(string Email, string Name);
public record UserResponse(int Id, string Email, string Name);

// With validation (non-destructive mutation)
var updated = request with { Name = "New Name" };
```

### Pattern Matching
```csharp
// Switch expression with patterns
string Describe(object obj) => obj switch
{
    int n when n > 0 => "positive",
    int n when n < 0 => "negative",
    int => "zero",
    string s => $"string: {s}",
    null => "null",
    _ => "unknown"
};
```

### Nullable Reference Types — Always On
```csharp
#nullable enable  // TOP OF EVERY FILE

// Nullable string: string? — must null-check before use
// Non-nullable string: string — guaranteed non-null
string? FindUser(int id) => id > 0 ? "Alice" : null;

// Safe usage — null-conditional and null-coalescing
var name = FindUser(1)?.ToUpper() ?? "Unknown";
```

---

## Minimal API Template (ASP.NET Core)

```csharp
#nullable enable
using FluentValidation;
using Microsoft.AspNetCore.Builder;
using Microsoft.Extensions.DependencyInjection;

var builder = WebApplication.CreateBuilder(args);

// Services
builder.Services.AddScoped<IUserService, UserService>();
builder.Services.AddValidatorsFromAssemblyContaining<Program>();

// Security — explicit CORS (never AllowAnyOrigin in production)
builder.Services.AddCors(options => options.AddDefaultPolicy(policy =>
    policy.WithOrigins("https://yourdomain.com")
          .AllowAnyMethod()
          .AllowAnyHeader()));

var app = builder.Build();
app.UseCors();

// Endpoints
app.MapGet("/users/{id:int}", async (int id, IUserService svc) =>
{
    var user = await svc.GetByIdAsync(id);
    return user is not null ? Results.Ok(user) : Results.NotFound();
});

app.MapPost("/users", async (CreateUserRequest req, IValidator<CreateUserRequest> validator, IUserService svc) =>
{
    var validation = await validator.ValidateAsync(req);
    if (!validation.IsValid) return Results.ValidationProblem(validation.ToDictionary());
    var created = await svc.CreateAsync(req);
    return Results.Created($"/users/{created.Id}", created);
});

app.Run();
```

---

## xUnit Test Template

```csharp
#nullable enable
using Moq;
using Xunit;

public class UserServiceTests
{
    private readonly Mock<IUserRepository> _repo = new();
    private readonly UserService _sut;  // sut = system under test

    public UserServiceTests()
    {
        _sut = new UserService(_repo.Object, Mock.Of<ILogger<UserService>>());
    }

    [Fact]
    public async Task GetByIdAsync_ExistingUser_ReturnsUser()
    {
        // Arrange
        var expected = new User(1, "alice@example.com", "Alice");
        _repo.Setup(r => r.GetByIdAsync(1)).ReturnsAsync(expected);

        // Act
        var result = await _sut.GetByIdAsync(1);

        // Assert
        Assert.Equal(expected, result);
    }

    [Theory]
    [InlineData(0)]
    [InlineData(-1)]
    public async Task GetByIdAsync_InvalidId_ReturnsNull(int id)
    {
        var result = await _sut.GetByIdAsync(id);
        Assert.Null(result);
    }
}
```

---

## IDisposable — using Pattern

```csharp
// Always use 'using' — disposes automatically even if exception thrown
await using var connection = new SqlConnection(connectionString);
await connection.OpenAsync();

// Or for synchronous resources
using var stream = File.OpenRead("file.txt");

// Implementing IAsyncDisposable (prefer over IDisposable for async resources)
public class MyService : IAsyncDisposable
{
    private readonly HttpClient _client = new();

    public async ValueTask DisposeAsync()
    {
        _client.Dispose();
        await Task.CompletedTask;
    }
}
```

---

## Secrets — Never Hardcode

```csharp
// ✗ NEVER
var connectionString = "Server=prod-db;Password=abc123";

// ✓ Environment variable
var connectionString = Environment.GetEnvironmentVariable("DB_CONNECTION")
    ?? throw new InvalidOperationException("DB_CONNECTION not set");

// ✓ .NET configuration (appsettings.json + env override)
var connectionString = builder.Configuration.GetConnectionString("DefaultConnection")
    ?? throw new InvalidOperationException("ConnectionStrings:DefaultConnection not set");
// Note: appsettings.json should use placeholder — real value in env or Key Vault
```

---

## REVIEW Checklist

| # | Check | What to look for | Report |
|---|-------|-----------------|--------|
| 1 | #nullable enable | Present at top of every .cs file | PASS/FAIL |
| 2 | No hardcoded secrets | Grep for password, secret, apikey, connectionstring with literal strings | PASS/FAIL |
| 3 | No string SQL | No string interpolation in SQL queries — EF Core or parameterized only | PASS/FAIL |
| 4 | No BinaryFormatter | Removed in .NET 9+ — use System.Text.Json | PASS/FAIL |
| 5 | Async methods awaited | No unawaited Task calls — check for fire-and-forget | PASS/WARN |
| 6 | No async void | `async void` outside event handlers — use `async Task` | PASS/WARN |
| 7 | IDisposable managed | Resources using IDisposable wrapped in `using` or disposed explicitly | PASS/WARN |
| 8 | Bare catch handled | `catch (Exception)` without rethrow or structured logging | PASS/WARN |
| 9 | CORS explicit | No `AllowAnyOrigin()` in production config | PASS/FAIL |
| 10 | Input validated | API endpoints validate input before persistence (FluentValidation) | PASS/WARN |
