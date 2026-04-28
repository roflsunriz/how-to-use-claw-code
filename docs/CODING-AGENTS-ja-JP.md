# CLIにフォーカスしたおすすめコーディングエージェント

このページは「CLI（端末）での利用を第一に考える」観点で、実務に適したコーディングエージェントをまとめたものです。

要約（短く）

- 最も柔軟でCLI中心の選択肢: OpenCode
- 公式・商用エコシステムを重視したいCLIユーザー: Codex CLI
- 軽量で日常編集向け: Aider
- ターミナルUX/TUI重視: Crush
- 拡張ワークフロー（MCP/Extensions）重視: Goose / Qwen Code

## 推奨トップ（CLI観点）

1. OpenCode — 多プロバイダ対応と豊富な拡張が魅力。ローカルモデルやカスタム provider を組み込みやすく、セッション管理や webfetch 等の CLI 機能が充実しています。
2. Codex CLI — OpenAI 系の公式 CLI。plugin / marketplace エコシステムと Ollama 等のローカル統合が強みで、商用環境に向きます。
3. Aider — 学習コストが小さく安定して使える軽量 CLI ツール。日常の編集やペアプロ向けに最短距離で使えます。
4. Crush — Charmbracelet 流の優れた TUI を持ち、端末での操作感を重視するユーザーに合います。
5. Goose / Qwen Code — MCP・Extensions・Skills を重視する場合の有力候補。multi-provider 機能で複数サブスクやプロバイダーをまたいだ運用に向きます。

## 各ツールのCLI観点ポイント

OpenCode

- CLI-first な拡張性: 75+ provider、plugins/skills、websearch/webfetch をネイティブでサポート。
- セッションの継続・フォーク・共有、auto-compaction などエージェント基盤が充実。
- 注意: compaction や model discovery、TUI の一部に粗さがあるため、運用ポリシーと監視が必要。

Codex CLI

- OpenAI 公式の CLI。`config.toml` や Ollama 統合でカスタム provider / ローカルモデルの運用が可能。
- plugin / marketplace の成長が速く、商用や組織利用での互換性が取りやすい。
- 注意: provider 切替後の履歴表示や compact 周りの挙動は事前検証を推奨。

Aider

- `/ask`、`/architect`、`/web`、`--read` 等、端末操作で使いやすいモードが揃う。
- Git/diff ベースの編集に馴染むため、既存の開発フローに入りやすい。
- 注意: MCP・プラグイン・Marketplaceなど拡張機構は弱め。

Crush

- 優れた TUI と multi-model/LSP 対応が特徴。端末上で快適な操作感を提供。
- 注意: websearch/webfetch や marketplace は限定的に見える。

Goose / Qwen Code

- Goose: MCP/extension directory/skills marketplace が強く、session persistence や provider ブリッジが充実。
- Qwen Code: `modelProviders` による multi-provider 切替、WebSearch/WebFetch、checkpointing などの CLI 機能を拡充中。
- 注意: auth・provider 接続周りに recent issues があるため運用テストは必須。

## 選び方（CLI向け）

- 自由度・拡張性を最大化したい: OpenCode
- 既存のOpenAI系サブスクや商用運用を活かしたい: Codex CLI
- 学習コストを抑え、安定して日常運用したい: Aider
- ターミナル体験（UX）を最優先する: Crush
- MCP/Extensions を中心にワークフローを組みたい: Goose / Qwen Code

## 評価・検証チェックリスト（導入前）

1. provider を差し替えたときの Web Tools / compaction / session の振る舞いを検証
2. local model（Ollama 等）を実運用で切り替えられるか確認
3. plugin/skill の導入手順と権限モデルを確認
4. セッション保存・resume/ fork の動作とストレージ要件を確認
