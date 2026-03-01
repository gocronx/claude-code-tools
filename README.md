# claude-code-tools

Personal Claude Code toolkit — agents, skills, hooks, rules, and commands for daily development.

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

[简体中文](README.zh-CN.md)

---

## Install

```bash
/plugin marketplace add gocronx/claude-code-tools
/plugin install claude-code-tools@claude-code-tools
```

> **Note:** Rules must be installed manually (Claude Code plugin system limitation):
> ```bash
> git clone https://github.com/gocronx/claude-code-tools.git
> cp -r claude-code-tools/rules/common/* ~/.claude/rules/
> cp -r claude-code-tools/rules/rust/* ~/.claude/rules/      # pick your stack
> cp -r claude-code-tools/rules/typescript/* ~/.claude/rules/
> ```

---

## Update

**Plugin** (agents, skills, commands) — reinstall to get the latest version:

```bash
/plugin uninstall claude-code-tools@claude-code-tools
/plugin install claude-code-tools@claude-code-tools
```

**Rules** — re-copy after pulling the latest:

```bash
cd claude-code-tools && git pull
cp -r rules/common/* ~/.claude/rules/
cp -r rules/rust/* ~/.claude/rules/       # pick your stack
cp -r rules/typescript/* ~/.claude/rules/
```

---

## What's Inside

### Agents

| Agent | Purpose | Model |
|-------|---------|-------|
| `planner` | Break down features into implementation steps | opus |
| `architect` | System design and architecture decisions | opus |
| `code-reviewer` | Code quality, security, best practices | sonnet |
| `security-reviewer` | OWASP Top 10, secrets detection, vulnerability analysis | sonnet |
| `tdd-guide` | Enforce write-tests-first methodology | sonnet |
| `refactor` | 重构清理专家，技术债处理、代码结构优化 | sonnet |
| `refactor-cleaner` | Remove dead code, consolidate duplicates | sonnet |
| `build-error-resolver` | Fix TypeScript/build errors with minimal changes | sonnet |
| `e2e-runner` | Generate and maintain Playwright E2E tests | sonnet |
| `doc-updater` | Keep documentation in sync with code | haiku |
| `go-reviewer` | Idiomatic Go, concurrency, error handling | sonnet |
| `go-build-resolver` | Fix Go build and vet errors | sonnet |
| `python-reviewer` | PEP 8, type hints, Pythonic patterns | sonnet |
| `rust-reviewer` | Ownership, unsafe code, async patterns | sonnet |
| `rust-build-resolver` | Fix borrow checker and compilation errors | sonnet |
| `database-reviewer` | PostgreSQL optimization, schema design, RLS | sonnet |

### Skills (58)

Language patterns, framework guides, architecture patterns, and workflow definitions.

- **Languages**: `rust-patterns`, `golang-patterns`, `python-patterns`, `cpp-coding-standards`, `java-coding-standards`, `swift-concurrency-6-2`
- **Frameworks**: `django-patterns`, `springboot-patterns`, `frontend-patterns`, `backend-patterns`
- **Workflows**: `tdd-workflow`, `security-review`, `continuous-learning-v2`, `verification-loop`
- **Infrastructure**: `docker-patterns`, `deployment-patterns`, `database-migrations`, `api-design`
- **Obsidian**: `obsidian-markdown`, `obsidian-bases`, `obsidian-cli`, `json-canvas`, `defuddle`
- **Reading**: `feynman`, `socratic`, `zettelkasten` *(requires Notion MCP)*

### Rules

Always-follow guidelines, auto-applied by file path:

```
rules/
├── common/       # Language-agnostic (immutability, git, testing, security)
├── typescript/
├── python/
├── golang/
├── rust/
└── swift/
```

### Commands

33 slash commands — `/plan`, `/tdd`, `/code-review`, `/build-fix`, `/e2e`, `/go-review`, `/python-review`, `/security-scan`, `/refactor-clean`, `/orchestrate`, and more.

---

## Acknowledgements

Built on top of:

- **[everything-claude-code](https://github.com/affaan-m/everything-claude-code)** by [@affaanmustafa](https://x.com/affaanmustafa) — the foundation of this toolkit
- **[obsidian-skills](https://github.com/kepano/obsidian-skills)** by [@kepano](https://github.com/kepano) — Obsidian vault skills