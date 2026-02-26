---
paths:
  - "**/*.rs"
  - "**/Cargo.toml"
  - "**/Cargo.lock"
---
# Rust Coding Style

> This file extends [common/coding-style.md](../common/coding-style.md) with Rust specific content.

## Formatting

- **rustfmt** is mandatory — no style debates
- **clippy** warnings are errors: `cargo clippy -- -D warnings`

## Naming Conventions

- `snake_case` for variables, functions, modules, files
- `PascalCase` for types, traits, enums
- `SCREAMING_SNAKE_CASE` for constants
- `'lifetime_name` for lifetimes (short, lowercase)

## Ownership

- Borrow (`&T`) for read-only access, borrow mutably (`&mut T`) only when needed
- Own (`T`) only when you need to store or transfer
- Avoid `.clone()` without thinking — redesign ownership first
- Prefer `&str` over `String` for function inputs

## Error Handling

- `?` everywhere — never `.unwrap()` in production code
- `anyhow` for applications, `thiserror` for libraries
- Always add context: `.with_context(|| format!("reading {path}"))?`

## Reference

See skill: `rust-patterns` for comprehensive Rust idioms, async patterns, and full-stack examples.
