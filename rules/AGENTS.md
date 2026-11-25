# Codex Orchestrator Rule

OpenAI Codex CLI (`codex`, default target model `gpt-6.2-sol`) is available on this machine for backend delegation and second-opinion reviews.

- When working on a full-stack project where Codex handles the backend and Antigravity handles architecture, API contracts, frontend, and end-to-end verification—or whenever delegating implementation or code review to Codex—read and follow the `codex-backend` skill (`skills/codex-backend/SKILL.md`).
- **Non-interactive CLI execution rule**: Always pass prompts via stdin (`-`) instead of only a positional prompt argument so `codex exec` does not hang waiting on an open stdin pipe:
  - PowerShell: `"..." | codex exec -m gpt-6.2-sol ... -`
  - bash / zsh: `codex exec -m gpt-6.2-sol ... - <<'EOF'`
