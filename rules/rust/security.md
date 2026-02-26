---
paths:
  - "**/*.rs"
  - "**/Cargo.toml"
  - "**/Cargo.lock"
---
# Rust Security

> This file extends [common/security.md](../common/security.md) with Rust specific content.

## Secret Management

```rust
// Read from environment, fail fast if missing
let db_url = std::env::var("DATABASE_URL")
    .expect("DATABASE_URL must be set");
```

## `unsafe` Policy

- Never use `unsafe` without a documented justification comment
- Each `unsafe` block must explain why it is sound
- Prefer safe abstractions (e.g. `bytemuck`, `zerocopy`) over raw `unsafe`

## Dependency Auditing

```bash
cargo audit          # Check for known vulnerabilities (cargo-audit)
cargo deny check     # License and advisory checks (cargo-deny)
```

## SQL Injection

Use SQLx parameterized queries exclusively — never format SQL strings with user input:

```rust
// GOOD: compile-time checked, parameterized
sqlx::query!("SELECT * FROM users WHERE id = $1", id)
    .fetch_one(&db).await?;
```

## Input Validation

Validate at the boundary — use types to enforce invariants so invalid data can't reach business logic.
