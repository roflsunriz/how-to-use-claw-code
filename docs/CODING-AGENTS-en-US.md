# CLI-focused Recommended Coding Agents

This page collects coding agents selected specifically for terminal-first (CLI) use and focuses on agents that are practical to configure, run, and extend entirely from the command line.

Quick summary

- Best overall CLI flexibility: OpenCode
- Best for official/commercial ecosystems on CLI: Codex CLI
- Best lightweight daily editing tool: Aider
- Best terminal UX/TUI experience: Crush
- Best for MCP/Extensions-driven workflows: Goose / Qwen Code

## Top recommendations (CLI perspective)

1. OpenCode — Broad multi-provider support and rich extension surface. Easy to integrate local models and custom providers; strong CLI primitives for sessions and webfetch.
2. Codex CLI — OpenAI's official CLI with plugin/marketplace momentum and Ollama/local integrations suitable for commercial deployments.
3. Aider — Lightweight, low-friction CLI-first assistant for everyday edits and pair-programming in the terminal.
4. Crush — Polished TUI and multi-model/LSP support for users who spend most of their time in a terminal.
5. Goose / Qwen Code — Strong MCP/extension ecosystems and multi-provider capabilities for building complex CLI workflows.

## Per-tool CLI notes

OpenCode

- CLI-first extensibility: supports 75+ providers, plugins/skills, native websearch/webfetch.
- Full session features (continue/fork/share) and auto-compaction policies.
- Caveat: compaction, model discovery, and some TUI areas still show rough edges — plan operational monitoring.

Codex CLI

- Official OpenAI CLI. `config.toml` and Ollama integration enable custom provider/local model workflows.
- Growing plugin/marketplace ecosystem and favorable for commercial/enterprise use.
- Caveat: verify history visibility and compact behavior after provider switches.

Aider

- Practical CLI modes (`/ask`, `/architect`, `/web`, `--read`) and strong Git/diff editing flow compatibility.
- Low adoption cost and predictable behavior for everyday edits.
- Caveat: limited MCP/plugins/marketplace extensibility compared to OpenCode or Codex CLI.

Crush

- Polished TUI from Charmbracelet, with multi-model and LSP support for terminal-first power users.
- Caveat: websearch/webfetch and marketplace features appear limited.

Goose / Qwen Code

- Goose: strong MCP/extension directory/skills marketplace and session persistence; can bridge CLI providers and existing subscriptions.
- Qwen Code: multi-provider `modelProviders` switching, MCP, Skills, WebSearch/WebFetch, and checkpointing rapidly expanding.
- Caveat: recent issues around auth and provider connections — validate stability in your environment.

## How to choose (CLI-focused)

- Maximize freedom and extensibility: OpenCode
- Leverage existing OpenAI subscriptions and enterprise readiness: Codex CLI
- Minimal friction daily CLI edits: Aider
- Terminal UX/TUI priority: Crush
- Build extension-driven workflows (MCP/Skills): Goose / Qwen Code

## Validation checklist (before adoption)

1. Test Web Tools / compaction / session behavior when swapping providers
2. Validate real-world switching for local models (Ollama etc.) and profile management
3. Confirm plugin/skill installation steps and permission model via CLI
4. Verify session persistence, resume/fork behavior and storage requirements
