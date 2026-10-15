# findings.md

how-to-use-claw-code の実現手段、共通指針の適用条件と調査知見の正本。作業前に全文読む。利用者向けの導入・使用方法はREADME、更新の手順はhow-to-update.md、実施結果と未検証事項はverification.mdを参照する。移した記述の確認日・条件・留保は元のまま保持し、文書移動を再実測と解釈しない。

## 共通指針の適用条件と代替検証

- 対象の共通ルール: 設計と実装方針／検証／更新手順
- 理由: 日本語・英語の文書サイトをZensicalで生成し、既存mkdocs.ymlとclassicテーマを使う。
- 適用範囲: 文書サイトの編集、ビルド、GitHub Pages公開
- 同等の品質を保つ手段・検証: requirements.txtとCIを正本にpython -m zensical build --strictで生成し、両言語のページと公開後の配信を分けて確認する。

新たな免除や操作許可を追加するものではない。AGENTS.mdの禁止・承認条件と共通指針は引き続き有効。

<a id="agents-1"></a>

## プロジェクト固有情報

- サイトの strict ビルドは `python -m zensical build --strict` を使う。既存の `mkdocs.yml` を Zensical が読み、classic テーマで公開する。

移動元: `AGENTS.md`。

<a id="readme-1"></a>

## サイト構成

`mkdocs.yml` のナビゲーションは次の通りです。

- Home
- Claw Code
- Install
- Providers
- Others

移動元: `README.md`。

<a id="readme-2"></a>

## ローカルで確認する

Python が使える環境で、次を実行します。

```powershell
python -m pip install -r requirements.txt
python -m zensical serve
```

ブラウザで `http://127.0.0.1:8000/` を開くと、ローカルプレビューを確認できます。

静的サイトとしてビルドする場合は、次を実行します。

```powershell
python -m zensical build --strict
```

ビルド結果は `site/` に出力されます。

移動元: `README.md`。

<a id="readme-3"></a>

## GitHub Pages で公開する

このリポジトリには、GitHub Pages へ自動デプロイするワークフローが含まれています。

- ワークフロー: `.github/workflows/deploy-mkdocs.yml`
- 対象ブランチ: `main`
- デプロイ方式: GitHub Pages Actions

GitHub 側では、リポジトリの `Settings` -> `Pages` で `Build and deployment` の `Source` を `GitHub Actions` に設定してください。

移動元: `README.md`。

<a id="readme-4"></a>

## リポジトリ構成

```txt
.
├── docs/
│   ├── CLAWCODE-en-US.md
│   ├── CLAWCODE-ja-JP.md
│   ├── INSTALL-en-US-win.md
│   ├── INSTALL-ja-JP-win.md
│   ├── OPENROUTER-en-US.md
│   ├── OPENROUTER-ja-JP.md
│   ├── LOCAL-MODELS-en-US.md
│   ├── LOCAL-MODELS-ja-JP.md
│   ├── CODING-AGENTS-en-US.md
│   ├── CODING-AGENTS-ja-JP.md
│   └── index.md
├── .github/workflows/deploy-mkdocs.yml
├── mkdocs.yml
├── requirements.txt
├── README.md
└── LICENSE
```

移動元: `README.md`。

<a id="readme-5"></a>

## 依存更新の自動処理

Dependabot は対象の依存関係を毎週確認します。patch／minor 更新は PR のチェック（CI）が成功した後に自動で squash merge されます。CI の失敗ジョブは 1 回だけ再実行します。再失敗した PR は残して手動で修正します。major 更新は手動で確認します。マージ後はデプロイ workflow を明示起動します。

移動元: `README.md`。

<a id="update-1"></a>

## Dependabot PR の更新

前提は `.github/dependabot.yml` と PR 用 CI（CI）です。更新 PR の head SHA と `gh pr checks <PR番号>` の結果を確認してください。patch／minor は全チェック成功後に自動取り込みされます。初回 CI 失敗は failed jobs のみを 1 回再実行し、再失敗した PR は残して手動で修正します。

設定を変えたときは `actionlint .github/workflows/dependabot-automation.yml` と実際の PR の Actions 結果を確認します。問題があれば呼び出し先の共通 workflow SHA を直前の検証済み値へ戻すコミットを push します。取り込まれた依存更新に問題があれば通常の revert コミットで復旧します。

CI 完了より Dependabot の分類が遅れる場合は、`callback_workflow_file` が指す呼び出し側 workflow を `workflow_dispatch` し、同じ PR 番号・head SHA・全チェックを再確認する。呼び出し側のファイル名を変える際はこの入力も一緒に更新する。

移動元: `how-to-update.md`。

<a id="verification-1"></a>

## Dependabot 自動処理（2026-09-23）

`.github/workflows/dependabot-automation.yml` を actionlint で検査し、PR 用 workflow 名（CI）と一致することを確認する。Dependabot の patch／minor かつ全 PR チェック成功の場合だけ取り込み、major・古い SHA・再失敗は残す。

実際の Dependabot PR がまだない場合、動作経路は未検証として扱う。実 PR 発生後に自動化ジョブ、CI の再試行、マージ結果を確認する。

大量の Dependabot PR により CI 完了より分類が遅れる場合でも、分類後の `workflow_dispatch` が現在の PR 番号と head SHA を照合して再評価する。別の作成者、古い SHA、未完了の CI はマージしない。

移動元: `verification.md`。

## 関連文書

- [利用者向けREADME](README.md)
- [更新と復旧](how-to-update.md)
- [検証結果と未検証範囲](verification.md)
- [変更履歴](CHANGELOG.md)
