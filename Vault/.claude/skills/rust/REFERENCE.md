# Rust Skill — Reference
Rust 2024 edition / Axum 0.8 / Tokio / thiserror + anyhow | Last verified: 2026-04-30

## Research Sources
- Rust 1.85.0 and 2024 edition: https://blog.rust-lang.org/2025/02/20/Rust-1.85.0/
- Rust web frameworks 2026: https://aarambhdevhub.medium.com/rust-web-frameworks-in-2026-axum-vs-actix-web-vs-rocket-vs-warp-vs-salvo-which-one-should-you-2db3792c79a2
- thiserror vs anyhow: https://google.github.io/comprehensive-rust/error-handling/thiserror-and-anyhow.html
- Rust security auditing 2026: https://sherlock.xyz/post/rust-security-auditing-guide-2026
- Rust testing patterns 2026: https://dasroot.net/posts/2026/03/rust-testing-patterns-reliable-releases/
- cargo-audit: https://crates.io/crates/cargo-audit
- cargo-deny: https://crates.io/crates/cargo-deny

---

## Rust 2024 — Key Patterns

### Result and Option — Always (not panic)
```rust
// ? operator propagates errors up the call stack
fn read_config(path: &str) -> anyhow::Result<Config> {
    let content = std::fs::read_to_string(path)?;  // ? on io::Error
    let config: Config = serde_json::from_str(&content)?;  // ? on serde error
    Ok(config)
}

// Option for nullable values
fn find_user(id: u32) -> Option<User> {
    users.iter().find(|u| u.id == id).cloned()
}

// Callers
let config = read_config("settings.json").expect("config must be valid at startup");
// .expect() in main/startup is acceptable — it's infallible once you control the path
// .unwrap() only in tests
```

### Error Handling — thiserror vs anyhow

```rust
// thiserror: library/API code — typed error variants callers can match
use thiserror::Error;

#[derive(Error, Debug)]
pub enum UserError {
    #[error("user {0} not found")]
    NotFound(u32),
    #[error("email already exists: {0}")]
    DuplicateEmail(String),
    #[error(transparent)]  // wraps underlying error
    Database(#[from] sqlx::Error),
}

// anyhow: application/CLI code — just propagate errors
use anyhow::{Context, Result};

fn main() -> Result<()> {
    let db = connect_db().context("failed to connect to database")?;
    // .context() adds a message to the error chain — better than bare ?
    Ok(())
}
```

### Ownership — Teach This First

```rust
// Ownership: each value has one owner; dropped when owner goes out of scope
let s = String::from("hello");  // s owns the string
let s2 = s;  // ownership moves to s2 — s is no longer valid
// println!("{}", s);  // ✗ compile error: s moved

// Borrowing: lend without transferring ownership
fn print_name(name: &str) {  // immutable borrow — caller keeps ownership
    println!("{}", name);
}
print_name(&s2);  // pass reference
// s2 still valid here

// Clone when you genuinely need a copy (not as a borrow-checker workaround)
let s3 = s2.clone();  // explicit deep copy — use sparingly
```

---

## Axum API Template

```rust
use axum::{
    extract::{Json, Path, State},
    http::StatusCode,
    response::IntoResponse,
    routing::{get, post},
    Router,
};
use serde::{Deserialize, Serialize};
use std::sync::Arc;
use tokio::net::TcpListener;

#[derive(Deserialize)]
struct CreateUserRequest {
    email: String,
    name: String,
}

#[derive(Serialize)]
struct UserResponse {
    id: u32,
    email: String,
    name: String,
}

// App state — pass shared resources via Arc
#[derive(Clone)]
struct AppState {
    db_url: String,  // from env, not hardcoded
}

#[tokio::main]
async fn main() -> anyhow::Result<()> {
    let state = Arc::new(AppState {
        db_url: std::env::var("DATABASE_URL")
            .expect("DATABASE_URL must be set"),
    });

    let app = Router::new()
        .route("/users", post(create_user))
        .route("/users/:id", get(get_user))
        .with_state(state);

    let listener = TcpListener::bind("0.0.0.0:8080").await?;
    axum::serve(listener, app).await?;
    Ok(())
}

async fn create_user(
    State(_state): State<Arc<AppState>>,
    Json(req): Json<CreateUserRequest>,
) -> impl IntoResponse {
    // validate req.email format here
    let user = UserResponse { id: 1, email: req.email, name: req.name };
    (StatusCode::CREATED, Json(user))
}

async fn get_user(Path(id): Path<u32>) -> impl IntoResponse {
    // query DB with parameterized SQL (sqlx)
    StatusCode::NOT_FOUND
}
```

---

## Cargo.toml Template

```toml
[package]
name = "my-app"
version = "0.1.0"
edition = "2024"

[dependencies]
axum = "0.8"
tokio = { version = "1", features = ["full"] }
serde = { version = "1", features = ["derive"] }
serde_json = "1"
anyhow = "1"
thiserror = "2"

[dev-dependencies]
axum-test = "15"  # HTTP integration test helpers

[profile.release]
overflow-checks = true  # catch integer overflow in release (disabled by default)
```

---

## Test Template

```rust
#[cfg(test)]
mod tests {
    use super::*;

    // Synchronous test
    #[test]
    fn test_validate_email() {
        assert!(validate_email("alice@example.com"));
        assert!(!validate_email("not-an-email"));
    }

    // Async test
    #[tokio::test]
    async fn test_create_user_endpoint() {
        // Arrange
        let app = build_test_app();  // build router without starting server
        let client = axum_test::TestClient::new(app);

        // Act
        let resp = client
            .post("/users")
            .json(&serde_json::json!({"email": "alice@example.com", "name": "Alice"}))
            .await;

        // Assert
        assert_eq!(resp.status(), StatusCode::CREATED);
    }
}
```

---

## Cargo Security Setup

```toml
# .cargo/audit.toml — configure cargo-audit
[advisories]
ignore = []  # list specific CVEs to ignore with justification comments

# deny.toml — cargo-deny configuration
[bans]
multiple-versions = "warn"  # flag duplicate deps

[advisories]
db-path = "~/.cargo/advisory-db"
db-urls = ["https://github.com/rustsec/advisory-db"]
```

```bash
# Run before release
cargo audit                         # check for CVEs
cargo clippy -- -D warnings         # lint as errors
cargo fmt --check                   # format check
cargo test                          # all tests pass
```

---

## REVIEW Checklist

| # | Check | What to look for | Report |
|---|-------|-----------------|--------|
| 1 | No unwrap() in non-test | `.unwrap()` calls outside `#[cfg(test)]` or `#[test]` blocks | PASS/FAIL |
| 2 | unsafe documented | `unsafe` blocks without comment explaining invariant | PASS/FAIL |
| 3 | No hardcoded secrets | Grep for hardcoded tokens, passwords, URLs with credentials | PASS/FAIL |
| 4 | Integer overflow checked | User-controlled arithmetic uses `checked_*` or `saturating_*` | PASS/WARN |
| 5 | cargo-audit configured | `.cargo/audit.toml` or `deny.toml` present | PASS/WARN |
| 6 | overflow-checks in release | `[profile.release] overflow-checks = true` in Cargo.toml | PASS/WARN |
| 7 | No panic! in prod paths | `panic!`, `todo!`, `unreachable!` in non-test production code | PASS/WARN |
| 8 | clone() justified | `.clone()` calls — are they semantically required or borrow workarounds? | PASS/WARN |
| 9 | No embedded/WASM scope | If `#![no_std]` or WASM targets appear — flag for specialist review | PASS/WARN |
| 10 | Input validated | Deserialized data validated before use (email format, ranges, etc.) | PASS/WARN |
