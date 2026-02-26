---
name: rust-patterns
description: Idiomatic Rust patterns for full-stack engineers — ownership, error handling with ?, async/Tokio, Axum web APIs, type-driven design, and zero-cost abstractions. Prioritizes concise, expressive code that leverages the type system.
origin: gocronx
---

# Rust Patterns

Idiomatic Rust for full-stack engineers. Let the type system do the heavy lifting — if it compiles, it's probably correct.

## When to Activate

- Writing new Rust code
- Designing APIs or data models
- Building async web services (Axum, Actix)
- Database access (SQLx)
- Reviewing Rust code for idioms and safety

## Core Philosophy

```rust
// Bad: fighting the borrow checker
// Good: design types so ownership flows naturally

// Bad: stringly-typed APIs
// Good: make invalid states unrepresentable

// Bad: unwrap() in production
// Good: ? everywhere, errors as values
```

## Ownership & Borrowing

### The Golden Rule

```rust
// Own it when you need to store it
// Borrow it when you just need to read it
// Borrow mutably only when you need to change it

fn process(data: &[u8]) -> usize { data.len() }      // borrow: just reading
fn transform(data: &mut Vec<u8>) { data.push(0); }   // mut borrow: modifying
fn consume(data: Vec<u8>) -> String { ... }           // own: need to store/return
```

### Clone Sparingly

```rust
// Bad: clone to satisfy borrow checker without thinking
let name = user.name.clone();
send_email(name);
display(user.name.clone()); // why are we cloning twice?

// Good: borrow where possible, clone where necessary
send_email(&user.name);
display(&user.name);
// Clone only when you truly need ownership
let name: String = user.name.clone(); // store for later
```

### Lifetime Elision — Trust the Compiler

```rust
// Explicit (unnecessary in simple cases)
fn first<'a>(s: &'a str) -> &'a str { &s[..1] }

// Idiomatic (compiler infers the lifetime)
fn first(s: &str) -> &str { &s[..1] }

// When you need explicit lifetimes: structs borrowing data
struct Parser<'a> {
    input: &'a str,
    pos: usize,
}
```

## Error Handling

### `?` is the Standard — No Exceptions

```rust
use anyhow::{Context, Result};

// Bad: manual match chains
fn read_config(path: &str) -> Result<Config> {
    let content = match fs::read_to_string(path) {
        Ok(s) => s,
        Err(e) => return Err(e.into()),
    };
    match toml::from_str(&content) {
        Ok(c) => Ok(c),
        Err(e) => Err(e.into()),
    }
}

// Good: ? propagates, .context() adds meaning
fn read_config(path: &str) -> Result<Config> {
    let content = fs::read_to_string(path)
        .with_context(|| format!("reading config {path}"))?;
    toml::from_str(&content).context("parsing config")
}
```

### Custom Error Types for Libraries

```rust
use thiserror::Error;

#[derive(Error, Debug)]
pub enum AppError {
    #[error("not found: {0}")]
    NotFound(String),

    #[error("unauthorized")]
    Unauthorized,

    #[error("database: {0}")]
    Database(#[from] sqlx::Error),
}

// Conversions are automatic via #[from]
// anyhow for applications, thiserror for libraries
```

### Option Chaining — No Null

```rust
// Bad: nested if let
if let Some(user) = get_user(id) {
    if let Some(email) = user.email {
        send(email);
    }
}

// Good: combinators
get_user(id)
    .and_then(|u| u.email)
    .map(send);

// Good: ? in functions returning Option
fn user_email(id: u64) -> Option<String> {
    get_user(id)?.email
}
```

## Type-Driven Design

### Make Invalid States Unrepresentable

```rust
// Bad: stringly typed, anything can go wrong
fn create_user(status: String) { ... }
create_user("actve".to_string()); // typo compiles fine

// Good: exhaustive by construction
#[derive(Debug, Clone, PartialEq)]
enum UserStatus { Active, Suspended, Deleted }

fn create_user(status: UserStatus) { ... }
// create_user(UserStatus::Actve); // compile error

// Encode state transitions in the type system
struct Unverified(String);
struct Verified(String);

fn verify(email: Unverified) -> Result<Verified> {
    validate(&email.0)?;
    Ok(Verified(email.0))
}

fn send_confirmation(email: Verified) { ... }
// Can't call send_confirmation with Unverified — compiler enforced
```

### Newtype for Zero-Cost Semantics

```rust
// Bad: mix up IDs at runtime
fn transfer(from: u64, to: u64, amount: u64) { ... }
transfer(amount, from, to); // oops, compiles fine

// Good: distinct types, zero runtime cost
struct UserId(u64);
struct Amount(u64);

fn transfer(from: UserId, to: UserId, amount: Amount) { ... }
// transfer(Amount(100), from, to); // compile error
```

### Builder Pattern with Typestate

```rust
struct Request<State> {
    url: String,
    _state: PhantomData<State>,
}

struct NoAuth;
struct WithAuth { token: String }

impl Request<NoAuth> {
    fn new(url: impl Into<String>) -> Self {
        Self { url: url.into(), _state: PhantomData }
    }
    fn auth(self, token: impl Into<String>) -> Request<WithAuth> {
        Request { url: self.url, _state: PhantomData }
    }
}

impl Request<WithAuth> {
    async fn send(self) -> Result<Response> { ... } // Only callable after auth
}
```

## Traits & Generics

### Trait Bounds — Express Intent

```rust
// Bad: concrete types everywhere, hard to test
fn log_event(event: AppEvent, logger: StdoutLogger) { ... }

// Good: accept anything that implements the trait
fn log_event(event: impl Into<String>, logger: &impl Logger) { ... }

// Good: where clause for complex bounds
fn process<T, E>(items: Vec<T>) -> Result<Vec<E>>
where
    T: Into<E> + Send,
    E: Serialize,
{ ... }
```

### Blanket Implementations

```rust
// Implement for all types satisfying constraints
trait Summary {
    fn summarize(&self) -> String;
}

impl<T: Display> Summary for T {
    fn summarize(&self) -> String {
        format!("{self}")  // Any Display type gets Summary for free
    }
}
```

### Iterator Adapters — Functional and Fast

```rust
// Bad: manual loops, verbose and error-prone
let mut results = Vec::new();
for item in items {
    if item.is_active() {
        results.push(item.score * 2);
    }
}

// Good: iterator chain — compiled to same assembly, zero overhead
let results: Vec<_> = items.iter()
    .filter(|i| i.is_active())
    .map(|i| i.score * 2)
    .collect();

// Parallel with rayon (one import, no rewrites)
use rayon::prelude::*;
let results: Vec<_> = items.par_iter()
    .filter(|i| i.is_active())
    .map(|i| i.score * 2)
    .collect();
```

## Async / Tokio

### Async from the Ground Up

```rust
// Cargo.toml
// tokio = { version = "1", features = ["full"] }
// axum = "0.7"

#[tokio::main]
async fn main() -> Result<()> {
    let app = Router::new()
        .route("/users/:id", get(get_user))
        .route("/users", post(create_user))
        .with_state(AppState::new().await?);

    let listener = TcpListener::bind("0.0.0.0:8080").await?;
    axum::serve(listener, app).await?;
    Ok(())
}
```

### Concurrent — Not Sequential

```rust
// Bad: sequential awaits, slow
async fn fetch_dashboard(user_id: u64) -> Result<Dashboard> {
    let user = get_user(user_id).await?;
    let posts = get_posts(user_id).await?;   // waits for user first (unnecessary)
    let stats = get_stats(user_id).await?;   // waits for posts first (unnecessary)
    Ok(Dashboard { user, posts, stats })
}

// Good: concurrent with join!
async fn fetch_dashboard(user_id: u64) -> Result<Dashboard> {
    let (user, posts, stats) = tokio::try_join!(
        get_user(user_id),
        get_posts(user_id),
        get_stats(user_id),
    )?;
    Ok(Dashboard { user, posts, stats })
}
```

### Axum — Typed HTTP

```rust
use axum::{extract::{Path, State}, Json, http::StatusCode};

// Extractors make invalid requests fail before your handler runs
async fn get_user(
    State(db): State<PgPool>,
    Path(id): Path<Uuid>,
) -> Result<Json<User>, AppError> {
    sqlx::query_as!(User, "SELECT * FROM users WHERE id = $1", id)
        .fetch_one(&db)
        .await
        .map(Json)
        .map_err(|e| match e {
            sqlx::Error::RowNotFound => AppError::NotFound(id.to_string()),
            e => AppError::Database(e),
        })
}

// AppError → HTTP response via IntoResponse
impl IntoResponse for AppError {
    fn into_response(self) -> Response {
        let (status, msg) = match &self {
            AppError::NotFound(id) => (StatusCode::NOT_FOUND, format!("not found: {id}")),
            AppError::Unauthorized   => (StatusCode::UNAUTHORIZED, "unauthorized".into()),
            AppError::Database(_)    => (StatusCode::INTERNAL_SERVER_ERROR, "internal error".into()),
        };
        (status, Json(json!({ "error": msg }))).into_response()
    }
}
```

### SQLx — Compile-Time SQL

```rust
// Queries are checked against live DB at compile time
// DATABASE_URL env var required for cargo build

#[derive(Debug, sqlx::FromRow, serde::Serialize)]
struct User {
    id: Uuid,
    email: String,
    created_at: DateTime<Utc>,
}

async fn list_users(db: &PgPool, limit: i64) -> Result<Vec<User>> {
    sqlx::query_as!(
        User,
        "SELECT id, email, created_at FROM users ORDER BY created_at DESC LIMIT $1",
        limit
    )
    .fetch_all(db)
    .await
    .context("listing users")
}

// Transactions: RAII — committed on Ok, rolled back on Err
async fn transfer(db: &PgPool, from: Uuid, to: Uuid, amount: i64) -> Result<()> {
    let mut tx = db.begin().await?;
    sqlx::query!("UPDATE accounts SET balance = balance - $1 WHERE id = $2", amount, from)
        .execute(&mut *tx).await?;
    sqlx::query!("UPDATE accounts SET balance = balance + $1 WHERE id = $2", amount, to)
        .execute(&mut *tx).await?;
    tx.commit().await?;
    Ok(())
}
```

## Performance Patterns

### Zero-Copy Where Possible

```rust
// Bad: allocate String just to parse it
fn parse_name(input: String) -> String {
    input.trim().to_string()
}

// Good: borrow the input, return a slice
fn parse_name(input: &str) -> &str {
    input.trim()
}

// Cow<str>: borrow when possible, own when necessary
use std::borrow::Cow;
fn normalize(s: &str) -> Cow<str> {
    if s.contains(' ') {
        Cow::Owned(s.replace(' ', "_"))
    } else {
        Cow::Borrowed(s) // zero allocation
    }
}
```

### Cache Locality — Prefer Vec over HashMap for Small Sets

```rust
// For n < ~20 elements, linear scan beats HashMap (cache friendly)
let tags: Vec<&str> = vec!["rust", "backend", "api"];
if tags.contains(&"rust") { ... }

// HashMap for larger sets
let tags: HashSet<&str> = ["rust", "backend", "api"].into();
if tags.contains("rust") { ... }
```

## Testing

### Unit Tests in the Same File

```rust
fn add(a: i32, b: i32) -> i32 { a + b }

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_add() {
        assert_eq!(add(2, 3), 5);
        assert_eq!(add(-1, 1), 0);
    }
}
```

### Async Tests with tokio::test

```rust
#[cfg(test)]
mod tests {
    use super::*;

    #[tokio::test]
    async fn test_get_user() {
        let db = setup_test_db().await;
        let user = get_user(&db, Uuid::new_v4()).await;
        assert!(matches!(user, Err(AppError::NotFound(_))));
    }
}
```

### Property Testing with proptest

```rust
use proptest::prelude::*;

proptest! {
    #[test]
    fn parse_never_panics(s in ".*") {
        // Should never panic, regardless of input
        let _ = parse_name(&s);
    }
}
```

## Essential Crates

| Crate | Use Case |
|-------|----------|
| `tokio` | Async runtime |
| `axum` | Web framework (type-safe, tower-based) |
| `sqlx` | Async SQL with compile-time query checking |
| `serde` + `serde_json` | Serialization |
| `anyhow` | Error handling in applications |
| `thiserror` | Error types in libraries |
| `tracing` | Structured async-aware logging |
| `rayon` | Data parallelism |
| `clap` | CLI argument parsing |
| `dotenv` / `config` | Configuration |

## Tooling

```bash
cargo fmt                  # Format (enforce in CI)
cargo clippy -- -D warnings # Lint (fail on warnings)
cargo test                 # Run tests
cargo build --release      # Optimized build
cargo doc --open           # Generate and open docs

# Watch mode for development
cargo install cargo-watch
cargo watch -x "test" -x "clippy"

# Faster linking (especially on macOS)
# .cargo/config.toml:
# [target.aarch64-apple-darwin]
# linker = "clang"
# rustflags = ["-C", "link-arg=-fuse-ld=lld"]
```

## Anti-Patterns

```rust
// ❌ unwrap() in production — panics at runtime
let user = get_user(id).unwrap();

// ✅ propagate with ?
let user = get_user(id)?;

// ❌ clone to silence borrow checker without thinking
let data = expensive_data.clone();

// ✅ redesign ownership, borrow where possible

// ❌ Arc<Mutex<T>> as default shared state
let state = Arc::new(Mutex::new(AppState::new()));

// ✅ Axum's State extractor, or message passing via channels

// ❌ String for everything
fn greet(name: String) -> String { format!("Hello, {name}") }

// ✅ &str for inputs, String only when you need ownership
fn greet(name: &str) -> String { format!("Hello, {name}") }
```

**Remember**: The borrow checker is not your enemy — it's the compiler catching bugs for free. When you fight it, you're usually fighting your own design. Redesign the ownership, not the lifetimes.
