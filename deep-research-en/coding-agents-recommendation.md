# Ranking of Coding Agents That Allow Free Model Swapping via API

## Executive Summary

In short: the overall winner is Cline, followed by OpenCode. Cline presents the strongest integrated set of MCP, Marketplace, Skills, task history/resume, checkpoints, SDKs, and IDE/CLI integration. However, it has notable known issues around checkpoints, terminal integration, MCP and edit reliability. OpenCode is the most flexible for provider switching, OpenAI-compatible endpoints, plugins, sessions and built-in websearch/webfetch; it is an excellent choice for terminal-centric, self-extendable workflows, though it has a high issue count and some rough edges in compaction and provider auto-discovery.

Recommended adoption order: Cline > OpenCode > Aider > Claw Code. Roo Code would be higher by feature completeness but has announced shutdown of Extension / Cloud / Router on 2026-05-15, making it unsuitable for new production adoption. Aider remains a stable CLI pair-programming assistant. Claw Code shows good OpenRouter/OpenAI-compatible support but is still immature in docs and missing some ACP features.

## Scope and Method

This ranking used the interpretation that "Claw-Code" refers to ultraworkers/claw-code and focused on coding agents where model swapping via API is clearly possible. Sources: official docs, OpenRouter connection docs, GitHub repos and issues. Feature presence was judged from docs; stability was inferred from open issues and release cadence. Scores used a 0–5 scale (0 absent, 3 practical, 5 strong). Missing documentation reduced scores conservatively.

## Ranking Table

Legend: Market = Marketplace, Compact = auto-compaction, WSearch = websearch, WFetch = webfetch.

| Rank | Agent | Score | MCP | Plugin | Skills | Market | Resume | Sessions | Compact | Tools | WSearch | WFetch | Read | Edit | OpenRouter / Swappable | Notes |
|---|---|---:|---|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | Cline | **86/100** | 5 | 3 | 5 | 5 | 4 | 5 | 4 | 5 | 2 | 2 | 5 | 4 | Native support; OpenRouter model list available. | Docs are very comprehensive; strong on Tasks/Resume/Checkpoints/Skills/MCP Marketplace. Known issues around checkpoints, terminal integration, and edit reliability. |
| 2 | OpenCode | **84/100** | 5 | 5 | 5 | 2 | 5 | 5 | 4 | 5 | 5 | 5 | 5 | 5 | Very strong; supports 75+ providers and adding arbitrary OpenAI-compatible providers. | Broad provider/plugin/skills/MCP/tools/sessions/server API support; marketplace and compaction have rough edges. |
| 3 | Roo Code | **67/100** | 5 | 2 | 5 | 5 | 3 | 3 | 5 | 5 | 0 | 0 | 5 | 5 | Native support; OpenRouter and OpenAI-compatible providers supported. | Feature-rich but announced product shutdown on 2026-05-15 — not recommended for new deployments. |
| 4 | Aider | **56/100** | 0 | 0 | 0 | 0 | 2 | 2 | 0 | 3 | 0 | 4 | 5 | 5 | Can accept OpenRouter slugs directly. | Mature CLI pair-programming assistant; weak on MCP/plugins/marketplace. |
| 5 | Claw Code | **51/100** | 2 | 2 | 3 | 0 | 4 | 4 | 0 | 4 | 0 | 0 | 4 | 4 | Supports OPENAI_BASE_URL + OPENAI_API_KEY for OpenRouter/OpenAI-compatible endpoints. | Sessions/resume/skills/plugin concepts are present but docs are scattered; not yet mature for production.

## Top Agents — Details

Cline: strongest combination of MCP, Skills, Marketplace, Sessions/Resume, Checkpoints, and integrated UX. Tasks are first-class objects with search and resume; each tool run can create shadow-Git checkpoints. SDKs expose session-as-context. Caveat: web tools often require Cline provider, so full feature parity when using OpenRouter/custom providers is not guaranteed.

OpenCode: strongest for provider freedom and extensibility. Clear configuration for providers (baseURL, headers, apiKey, model maps) and claims 75+ providers. Plugins support npm and local files; skills discovered via SKILL.md. Sessions support --continue/--session/--fork and compaction config is available. Compaction-related issues are the biggest practical concern.

Roo Code: rich feature surface including OpenRouter/OpenAI-compatible providers, profiles, modes, skills, marketplace, checkpoints and intelligent context condensing. However, official shutdown announcement removes it from new adoption consideration.

Aider: reliable CLI tool with LiteLLM provider flexibility, /ask, /architect, /web, and --read. Good for straightforward coding workflows — not a platform extension engine.

Claw Code: promising for lab use; supports OpenRouter/OpenAI-compatible endpoints via environment variables. Docs are scattered and some features are incomplete.

## Adoption Scenarios

- If you must pick one platform: Cline. Best balance of integrated features for teams.
- If you prioritize terminal/server-side provider freedom and extensibility: OpenCode.
- If you want a conservative CLI-focused coding assistant: Aider.
- Roo Code: migrate existing users away; don't adopt new.
- Claw Code: experimental/lab follow-up; not yet a production mainline.

## Open Questions and Limitations

Scoring errs conservatively where docs are silent, which penalizes Aider and Claw Code. Also, "usable with OpenRouter" does not guarantee every agent feature works identically when running via OpenRouter; provider-dependent tool behaviors (e.g., web tools) are the main caveat.

(End of file)
