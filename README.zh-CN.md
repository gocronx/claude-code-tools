# claude-code-tools

个人 Claude Code 工具库 — 包含 agents、skills、hooks、rules 和 commands，用于日常开发。

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

---

## 安装

```bash
/plugin marketplace add gocronx/claude-code-tools
/plugin install claude-code-tools@claude-code-tools
```

> **注意：** Rules 需手动安装（Claude Code 插件系统限制）：
> ```bash
> git clone https://github.com/gocronx/claude-code-tools.git
> cp -r claude-code-tools/rules/common/* ~/.claude/rules/
> cp -r claude-code-tools/rules/rust/* ~/.claude/rules/      # 按需选择
> cp -r claude-code-tools/rules/typescript/* ~/.claude/rules/
> ```

---

## 更新

**插件**（agents、skills、commands）— 重新安装即可获取最新版本：

```bash
/plugin uninstall claude-code-tools
/plugin install claude-code-tools@claude-code-tools
```

**Rules** — 拉取最新代码后重新复制：

```bash
cd claude-code-tools && git pull
cp -r rules/common/* ~/.claude/rules/
cp -r rules/rust/* ~/.claude/rules/       # 按需选择
cp -r rules/typescript/* ~/.claude/rules/
```

---

## 内容

### Agents

| Agent | 用途 | Model |
|-------|------|-------|
| `planner` | 功能拆解为实现步骤 | opus |
| `architect` | 系统设计与架构决策 | opus |
| `code-reviewer` | 代码质量、安全、最佳实践 | sonnet |
| `security-reviewer` | OWASP Top 10、密钥检测、漏洞分析 | sonnet |
| `tdd-guide` | 强制测试先行方法论 | sonnet |
| `refactor-cleaner` | 清除死代码、合并重复 | sonnet |
| `build-error-resolver` | 修复 TypeScript/构建错误，改动最小 | sonnet |
| `fullstack-engineer` | 端到端功能实现，覆盖前后端和数据库全链路 | sonnet |
| `e2e-runner` | 生成和维护 Playwright E2E 测试 | sonnet |
| `doc-updater` | 保持文档与代码同步 | haiku |
| `go-reviewer` | 惯用 Go、并发、错误处理 | sonnet |
| `go-build-resolver` | 修复 Go 构建和 vet 错误 | sonnet |
| `python-reviewer` | PEP 8、类型注解、Pythonic 模式 | sonnet |
| `rust-reviewer` | 所有权、unsafe 代码、async 模式 | sonnet |
| `rust-build-resolver` | 修复借用检查器和编译错误 | sonnet |
| `database-reviewer` | PostgreSQL 优化、schema 设计、RLS | sonnet |

### Skills（59 个）

语言模式、框架指南、架构模式和工作流定义。精选：

- **语言**：`rust-patterns`、`golang-patterns`、`python-patterns`、`cpp-coding-standards`、`java-coding-standards`、`swift-concurrency-6-2`
- **框架**：`django-patterns`、`springboot-patterns`、`frontend-patterns`、`backend-patterns`
- **工作流**：`tdd-workflow`、`security-review`、`continuous-learning-v2`、`verification-loop`
- **基础设施**：`docker-patterns`、`deployment-patterns`、`database-migrations`、`api-design`
- **Obsidian**：`obsidian-markdown`、`obsidian-bases`、`obsidian-cli`、`json-canvas`、`defuddle`
- **读书**：`feynman`、`socratic`、`zettelkasten`（需要 Notion MCP）

### Rules

按文件路径自动应用的编码规范：

```
rules/
├── common/       # 语言无关（不可变性、git、测试、安全）
├── typescript/
├── python/
├── golang/
├── rust/
└── swift/
```

### Commands

32 个斜杠命令 — `/plan`、`/tdd`、`/code-review`、`/build-fix`、`/e2e`、`/go-review`、`/python-review`、`/security-scan`、`/refactor-clean`、`/orchestrate` 等。

---

## 致谢

基于以下项目构建：

- **[everything-claude-code](https://github.com/affaan-m/everything-claude-code)** by [@affaanmustafa](https://x.com/affaanmustafa) — 本工具库的基础
- **[obsidian-skills](https://github.com/kepano/obsidian-skills)** by [@kepano](https://github.com/kepano) — Obsidian vault skills
