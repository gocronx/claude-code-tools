# CLAUDE.md

This file provides guidance to Claude Code when working in this repository.

## Project Overview

`claude-code-tools` is a personal Claude Code plugin by gocronx — a curated collection of agents, skills, commands, hooks, and rules for daily development.

## Repository Structure

```
claude-code-tools/
├── agents/       # Subagents for delegation (16 agents)
├── skills/       # Workflow definitions and domain knowledge (58 skills)
├── commands/     # Slash commands (/tdd, /plan, /code-review, etc.)
├── hooks/        # Trigger-based automations (hooks.json + scripts/)
├── scripts/      # Node.js hook implementations
├── rules/        # Always-follow guidelines, organized by language
│   ├── common/
│   ├── typescript/
│   ├── python/
│   ├── golang/
│   ├── rust/
│   └── swift/
└── .claude-plugin/plugin.json
```

## File Formats

**Agents** — YAML frontmatter + body starting with "You are...":
```markdown
---
name: agent-name
description: one-line description
tools: ["Read", "Grep", "Glob", "Bash"]
model: sonnet
---

You are a ...
```

**Skills** — `skills/<name>/SKILL.md` with optional frontmatter:
```markdown
---
name: skill-name
description: one-line description
origin: gocronx
---

# Skill content...
```

**Rules** — YAML `paths` frontmatter for auto-activation:
```markdown
---
paths: ["**/*.rs", "**/Cargo.toml"]
---

# Rule content...
```

## Key Conventions

- Agent tools: JSON array `["Read", "Grep"]`, not comma-separated string
- Agent body: starts directly with "You are...", no `# Title` heading
- File naming: lowercase with hyphens (`rust-reviewer.md`, `tdd-workflow.md`)
- Rust stack: Tokio, Axum, SQLx, anyhow/thiserror, serde
- Reading skills (`feynman`, `socratic`, `zettelkasten`) require Notion MCP
