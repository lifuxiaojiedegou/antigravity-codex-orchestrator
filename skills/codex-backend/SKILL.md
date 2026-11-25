---
name: codex-backend
description: >-
  Orchestrate OpenAI Codex CLI (gpt-6.2-sol) for backend development and second-opinion
  code reviews while Antigravity leads architecture, API contracts, frontend development,
  and end-to-end verification. Use when building a full-stack project with Codex on the
  backend, delegating backend tasks to Codex, or requesting a Codex code review.
---

# Codex Backend Orchestration & Collaboration

Use this workflow when pairing **Antigravity** (coordinator, API contracts, frontend, E2E verification) with **Codex CLI (`gpt-6.2-sol`)** (backend implementation, backend tests, and second-opinion reviews).

## Division of Responsibilities

- **Antigravity (Coordinator + Frontend + E2E)**:
  - Clarifies requirements, architecture, tech stack, and directory boundaries.
  - Authors and owns the API contract (e.g., `docs/api-contract.md` or `openapi.yaml`).
  - Dispatches scoped backend tasks to Codex CLI, reviews/verifies Codex outputs, and builds `frontend/`.
  - Starts backend + frontend services and verifies live end-to-end behavior (browser subagent + API checks).
- **Codex CLI (`gpt-6.2-sol`)**:
  - Implements backend code and backend unit/integration tests strictly inside the backend scope (e.g., `backend/`).
  - Must NOT modify `frontend/` or unilaterally alter the API contract; if a contract change is needed, it must propose the change in its final response for Antigravity to approve and update.

## Non-Interactive Execution Rules (Cross-Platform)

1. **Always pipe the prompt via stdin with `-`**:
   In non-TTY tool shells, passing only a positional prompt can leave `codex exec` waiting on an open stdin pipe (`Reading additional input from stdin...`). Always feed the prompt via stdin and pass `-`:
   - **Windows (PowerShell)**:
     ```powershell
     # Short prompt
     "Your prompt" | codex exec -m gpt-6.2-sol -s workspace-write -C "<workspace>" -

     # Multi-line brief from a file
     Get-Content -Raw "<brief-file>" | codex exec -m gpt-6.2-sol -s workspace-write -C "<workspace>" -
     ```
   - **macOS / Linux (bash / zsh)**:
     ```bash
     # Multi-line brief via heredoc
     codex exec -m gpt-6.2-sol -s workspace-write -C "<workspace>" - <<'EOF'
     Your multi-line task brief here...
     EOF
     ```
2. **Sandbox & repo flags**:
   - Use `-s workspace-write` (never the deprecated `--full-auto`) for backend coding tasks.
   - Use `-s read-only` for inspection or read-only checks.
   - Add `--skip-git-repo-check` if the target workspace is not initialized as a Git repository.
   - Optionally pass `-o "<output-file>"` to capture Codex's final message cleanly.
   - When dispatching nontrivial backend tasks in the background, set an appropriate notification timeout and wait for completion rather than polling.

## Full-Stack Workflow (Contract-First)

1. **Align & Lock Contract**:
   - Confirm requirements, tech stack, and directory layout (prefer a single Monorepo root containing `backend/`, `frontend/`, and `docs/api-contract.md` so `-C <root>` gives Codex direct read access to the contract and write access to `backend/`).
   - For a brand-new project from scratch, run `git init` early so `codex exec`, `codex exec review`, and diff tracking work cleanly (otherwise pass `--skip-git-repo-check`).
   - Write `docs/api-contract.md` (or `openapi.yaml`) covering endpoints, HTTP methods, request/response schemas, status codes, auth, and CORS expectations.
2. **Dispatch Backend Work to Codex**:
   - Include in the brief:
     1. Exact path to the API contract file to read and follow.
     2. Writable scope (`backend/` only; do not edit `frontend/` or `docs/api-contract.md`).
     3. Required backend tests / health checks to run and pass before finishing.
     4. Expected summary: files changed, tests executed + results, how to start the server, and any suggested contract adjustments.
3. **Inspect & Accept Backend**:
   - Read the backend files Codex created/edited and verify that tests actually passed and routes/schemas match the contract.
   - If fixes are needed, send a targeted follow-up via `codex exec -m gpt-6.2-sol -s workspace-write -C "<workspace>" -` (or `codex exec resume --last` when continuing the same thread).
4. **Build Frontend & Run End-to-End Verification**:
   - Implement `frontend/` against the verified API contract.
   - Start both backend and frontend servers and verify real user flows (browser subagent for UI/console/network checks, plus HTTP checks as needed).
   - If E2E testing reveals a backend bug, dispatch a focused fix brief to Codex and re-verify after it finishes.

## Second-Opinion Code Review Mode

When asked to have Codex review current changes:

- **Uncommitted changes in a Git repo**:
  - PowerShell:
    ```powershell
    "Review the uncommitted changes strictly for bugs, edge cases, security issues, and contract mismatches." | codex exec review -m gpt-6.2-sol --uncommitted -
    ```
  - bash / zsh:
    ```bash
    codex exec review -m gpt-6.2-sol --uncommitted - <<'EOF'
    Review the uncommitted changes strictly for bugs, edge cases, security issues, and contract mismatches.
    EOF
    ```
- **Against a branch or outside Git**:
  - Branch diff: pass `--base main` to `codex exec review -m gpt-6.2-sol --base main -`.
  - Non-Git / read-only review: pass `-s read-only --skip-git-repo-check --ephemeral -` to `codex exec`.
