# Go Skill — Reference
Go 1.26 / Gin v1 / testify / golangci-lint | Last verified: 2026-04-30

## Research Sources
- Go release notes: https://go.dev/doc/devel/release
- Go 1.26 release: https://go.dev/blog/go1.26
- Go error handling 2026: https://oneuptime.com/blog/post/2026-02-03-go-error-handling/view
- Go fmt.Errorf zero-cost: https://hitzhangjie.medium.com/go-1-26-stop-worrying-about-fmt-errorf-vs-errors-new-a6a47d2bc5d2
- Go secure APIs OWASP: https://oneuptime.com/blog/post/2026-01-07-go-secure-apis-owasp-top-10/view
- Table-driven tests 2026: https://dasroot.net/posts/2026/01/go-testing-excellence-table-driven-tests-mocking/
- golangci-lint 2026: https://tenthirtyam.org/dispatches/2026/04/18/a-deep-dive-into-golangci-lint/
- Gin vs Echo vs Fiber: https://encore.dev/articles/gin-vs-echo-vs-fiber

---

## Error Handling — Go Standard

```go
// Wrap errors with %w to preserve the chain
func getUser(id int) (*User, error) {
    row := db.QueryRow("SELECT id, email FROM users WHERE id = $1", id)
    var u User
    if err := row.Scan(&u.ID, &u.Email); err != nil {
        return nil, fmt.Errorf("getUser %d: %w", id, err)
    }
    return &u, nil
}

// Check specific error types
if errors.Is(err, sql.ErrNoRows) {
    return nil, ErrNotFound  // sentinel error
}

// Extract concrete error type
var pgErr *pgconn.PgError
if errors.As(err, &pgErr) && pgErr.Code == "23505" {
    return nil, ErrAlreadyExists
}
```

---

## Gin API Template

```go
package main

import (
    "net/http"
    "os"

    "github.com/gin-contrib/cors"
    "github.com/gin-gonic/gin"
)

type CreateUserRequest struct {
    Email string `json:"email" binding:"required,email"`
    Name  string `json:"name"  binding:"required,min=2"`
}

type UserResponse struct {
    ID    int    `json:"id"`
    Email string `json:"email"`
    Name  string `json:"name"`
}

func main() {
    // gin.New() + explicit middleware for prod (not gin.Default())
    r := gin.New()
    r.Use(gin.Recovery())
    r.Use(gin.Logger())

    // Explicit CORS — never AllowAllOrigins in production
    r.Use(cors.New(cors.Config{
        AllowOrigins:     []string{os.Getenv("ALLOWED_ORIGIN")},
        AllowMethods:     []string{"GET", "POST", "PUT", "DELETE"},
        AllowHeaders:     []string{"Content-Type", "Authorization"},
        AllowCredentials: true,
    }))

    r.POST("/users", createUser)
    r.GET("/users/:id", getUser)

    port := os.Getenv("PORT")
    if port == "" {
        port = "8080"
    }
    r.Run(":" + port)  // port from env, not hardcoded
}

func createUser(c *gin.Context) {
    var req CreateUserRequest
    if err := c.ShouldBindJSON(&req); err != nil {
        c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
        return
    }
    // Insert logic here — use parameterized queries
    c.JSON(http.StatusCreated, UserResponse{ID: 1, Email: req.Email, Name: req.Name})
}
```

---

## go.mod Template

```
module github.com/username/myapp

go 1.26

require (
    github.com/gin-gonic/gin v1.10.0
    github.com/gin-contrib/cors v1.7.3
    github.com/stretchr/testify v1.9.0
)
```

---

## Table-Driven Test Template

```go
package handlers_test

import (
    "net/http"
    "net/http/httptest"
    "strings"
    "testing"

    "github.com/gin-gonic/gin"
    "github.com/stretchr/testify/assert"
    "github.com/stretchr/testify/require"
)

func TestCreateUser(t *testing.T) {
    gin.SetMode(gin.TestMode)

    tests := []struct {
        name       string
        body       string
        wantStatus int
    }{
        {
            name:       "valid request",
            body:       `{"email":"alice@example.com","name":"Alice"}`,
            wantStatus: http.StatusCreated,
        },
        {
            name:       "missing email",
            body:       `{"name":"Alice"}`,
            wantStatus: http.StatusBadRequest,
        },
        {
            name:       "invalid email",
            body:       `{"email":"not-an-email","name":"Alice"}`,
            wantStatus: http.StatusBadRequest,
        },
    }

    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            // Arrange
            r := gin.New()
            r.POST("/users", createUser)
            w := httptest.NewRecorder()
            req, err := http.NewRequest(http.MethodPost, "/users",
                strings.NewReader(tt.body))
            require.NoError(t, err)
            req.Header.Set("Content-Type", "application/json")

            // Act
            r.ServeHTTP(w, req)

            // Assert
            assert.Equal(t, tt.wantStatus, w.Code)
        })
    }
}
```

---

## Context Pattern

```go
// Always pass context as first param for I/O operations
func fetchUser(ctx context.Context, id int) (*User, error) {
    // DB query respects context cancellation/timeout
    row := db.QueryRowContext(ctx,
        "SELECT id, email FROM users WHERE id = $1", id)
    var u User
    if err := row.Scan(&u.ID, &u.Email); err != nil {
        return nil, fmt.Errorf("fetchUser %d: %w", id, err)
    }
    return &u, nil
}

// HTTP handler — extract context from request
func getUser(c *gin.Context) {
    ctx := c.Request.Context()  // carries deadline and cancellation
    user, err := fetchUser(ctx, 1)
    if err != nil {
        c.JSON(http.StatusInternalServerError, gin.H{"error": "internal error"})
        return
    }
    c.JSON(http.StatusOK, user)
}
```

---

## REVIEW Checklist

| # | Check | What to look for | Report |
|---|-------|-----------------|--------|
| 1 | No swallowed errors | `_ = someFunc()` or `if err != nil { }` with empty body | PASS/FAIL |
| 2 | Errors wrapped | Returned errors use `fmt.Errorf("...: %w", err)` | PASS/WARN |
| 3 | Context propagated | I/O functions accept `ctx context.Context` as first param | PASS/WARN |
| 4 | No hardcoded secrets | Grep for hardcoded connection strings, tokens, passwords | PASS/FAIL |
| 5 | Parameterized SQL | No string concatenation in SQL queries — `$1` placeholders | PASS/FAIL |
| 6 | defer rows.Close() | Every `db.Query` or `db.QueryContext` has `defer rows.Close()` | PASS/WARN |
| 7 | Goroutine cleanup | goroutines have context cancellation or done channel | PASS/WARN |
| 8 | No AllowAllOrigins | CORS config never uses `AllowAllOrigins: true` | PASS/FAIL |
| 9 | Interface nil trap | Returning concrete nil through interface — assign to typed var | PASS/WARN |
| 10 | go.sum committed | `go.sum` present and committed alongside `go.mod` | PASS/FAIL |
