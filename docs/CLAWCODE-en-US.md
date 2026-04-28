# About Claw Code

This document provides a concise overview and important notes about Claw Code.

## Overview

- Claw Code is a clean-room implementation inspired by the leaked Claude Code. The repository is implemented independently and is not a copy of the leaked source.
- There is Python source code in the GitHub repository, but it serves mainly as a blueprint (prototype/reference). The primary implementation is written in Rust.

## Distribution & Build

- Official release builds are not distributed. If you want to use Claw Code, you need to build it from source yourself.

## Providers & Compatibility

- By swapping the API key and endpoint, you can use providers other than Anthropic. Any provider that exposes an OpenAI-compatible API should generally work.
- Running models locally is also possible, but requires powerful GPUs (and appropriate hardware) to be practical.

## Notes and Caveats

- Compared to OpenCode or Codex CLI, Claw Code still has rough edges; you may need manual adjustments for operation and compatibility.
- Note that this project's source code is independently implemented and not a copy of the leaked source code.

---

This document is intended as a short overview. For detailed build instructions, configuration examples, and provider usage examples (e.g., OpenRouter), see the relevant pages (INSTALL, OPENROUTER, LOCAL-MODELS, etc.).
