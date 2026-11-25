# Codex Orchestrator (Antigravity Plugin)

An Antigravity plugin that lets **Antigravity** act as the project coordinator, API contract owner, frontend developer, and E2E verifier while delegating backend implementation, backend tests, and second-opinion code reviews to **OpenAI Codex CLI (`gpt-6.2-sol`)**.

一个用于 **Antigravity** 的插件：让 Antigravity 负责整体架构、API 接口契约、前端开发与真实端到端联调验收，同时通过本地 **OpenAI Codex CLI (`gpt-6.2-sol`)** 自动调度 Codex 完成后端开发、后端测试与代码交叉审查。

---

## Why Use This? / 为什么这么搭配？

- **Contract-First Full-Stack Development（契约先行）**: Antigravity locks `docs/api-contract.md` (or `openapi.yaml`) and enforces directory boundaries (`backend/` vs `frontend/`) before dispatching backend work to Codex.
- **Independent Context Windows（上下文互不挤占）**: Codex runs in its own CLI process and context window, returning concise results instead of bloating Antigravity's main conversation.
- **Cross-Model Second Opinion（异构模型交叉审查）**: Use one model family to implement and the other to review diffs for edge cases, security bugs, and contract mismatches.
- **Non-TTY Stdin Fix Built In（内置防卡死规范）**: Automatically avoids the common `Reading additional input from stdin...` hang when invoking `codex exec` from agent shell tools on Windows/macOS/Linux.

---

## Prerequisites / 前置要求

1. **Antigravity** (Desktop App, IDE, or CLI)
2. **Node.js / npm** (or `winget` / Homebrew) and **OpenAI Codex CLI**:
   ```bash
   npm install -g @openai/codex
   ```
3. Sign in once with your ChatGPT/Codex account or API key:
   ```bash
   codex
   ```
4. *(Optional)* Verify non-interactive execution works in your terminal:
   ```bash
   # PowerShell
   "Reply with OK" | codex exec -m gpt-6.2-sol -s read-only --skip-git-repo-check --ephemeral -

   # bash / zsh
   codex exec -m gpt-6.2-sol -s read-only --skip-git-repo-check --ephemeral - <<'EOF'
   Reply with OK
   EOF
   ```

---

## Installation / 安装方式

Copy or clone this directory into Antigravity's global plugins folder:

- **Windows**: `%USERPROFILE%\.gemini\config\plugins\codex-orchestrator\`
- **macOS / Linux**: `~/.gemini/config/plugins/codex-orchestrator/`

Example:

```bash
# macOS / Linux
git clone https://github.com/lifuxiaojiedegou/antigravity-codex-orchestrator ~/.gemini/config/plugins/codex-orchestrator
```

```powershell
# Windows (PowerShell)
git clone https://github.com/lifuxiaojiedegou/antigravity-codex-orchestrator "$env:USERPROFILE\.gemini\config\plugins\codex-orchestrator"
```

Antigravity discovers plugins under `~/.gemini/config/plugins/` automatically (you can toggle **Codex Orchestrator** on/off anytime from Antigravity's Customizations / Plugins page).

---

## Plugin Structure / 目录结构

```text
codex-orchestrator/
├── plugin.json                        # Antigravity plugin manifest
├── rules/
│   └── AGENTS.md                      # Lightweight always-on trigger & CLI stdin rule
├── skills/
│   └── codex-backend/
│       └── SKILL.md                   # Detailed full-stack orchestration & review runbook
├── LICENSE
└── README.md
```

---

## Example Prompts / 使用示例

- **Full-stack from scratch / 从零建全栈项目**:
  > "Build a task tracker web app. You design the architecture, lock `docs/api-contract.md`, delegate the backend in `backend/` to Codex CLI (`gpt-6.2-sol`), build `frontend/`, and verify everything end-to-end."
- **Take over an existing repo / 接手现有项目**:
  > "Read `AGENTS.md` and `docs/api-contract.md`, inspect the repo first without editing, and split the next milestone between Codex (backend) and yourself (frontend + E2E)."
- **Code review / 交叉代码审查**:
  > "Ask Codex (`gpt-6.2-sol`) to run a strict code review on my current uncommitted changes and summarize any issues."

---

## Customization / 自定义

- **Default Model / 默认模型**: Edit `-m gpt-6.2-sol` in `rules/AGENTS.md` and `skills/codex-backend/SKILL.md` if you prefer another Codex model (or omit `-m` to inherit `~/.codex/config.toml`).
- **Directory Layout / 目录约定**: Defaults to a monorepo layout (`backend/`, `frontend/`, `docs/api-contract.md`), which keeps `-C <repo>` scoped cleanly to one workspace root.

## License

[MIT](./LICENSE)
