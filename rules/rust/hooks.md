---
paths:
  - "**/*.rs"
  - "**/Cargo.toml"
  - "**/Cargo.lock"
---
# Rust Hooks

> This file extends [common/hooks.md](../common/hooks.md) with Rust specific content.

## PostToolUse Hooks

Configure in `~/.claude/settings.json`:

- **rustfmt**: Auto-format `.rs` files after edit
- **clippy**: Run `cargo clippy -- -D warnings` after editing `.rs` files
- **cargo check**: Fast type-check without full build after edits
