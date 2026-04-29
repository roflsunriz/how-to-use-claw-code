# How to Use Claw Code

Claw Code の導入・利用方法をまとめたドキュメントリポジトリです。

MkDocs で構成されたサイトを、英語版と日本語版の両方で管理しています。公開サイトは GitHub Pages で配信しています。

## ドキュメント

- 公開サイト: https://roflsunriz.github.io/how-to-use-claw-code/
- [Claw Code について](docs/CLAWCODE-ja-JP.md)
- [Claw Code のインストール](docs/INSTALL-ja-JP-win.md)
- [OpenRouter で使う](docs/OPENROUTER-ja-JP.md)
- [ローカルモデルを使う](docs/LOCAL-MODELS-ja-JP.md)
- [おすすめのコーディングエージェント](docs/CODING-AGENTS-ja-JP.md)

英語版は同名の `-en-US.md` ファイルにあります。

## サイト構成

`mkdocs.yml` のナビゲーションは次の通りです。

- Home
- Claw Code
- Install
- Providers
- Others

## ローカルで確認する

Python が使える環境で、次を実行します。

```powershell
python -m pip install -r requirements.txt
python -m mkdocs serve --livereload --dirty
```

ブラウザで `http://127.0.0.1:8000/` を開くと、ローカルプレビューを確認できます。

静的サイトとしてビルドする場合は、次を実行します。

```powershell
python -m mkdocs build --strict
```

ビルド結果は `site/` に出力されます。

## GitHub Pages で公開する

このリポジトリには、GitHub Pages へ自動デプロイするワークフローが含まれています。

- ワークフロー: `.github/workflows/deploy-mkdocs.yml`
- 対象ブランチ: `main`
- デプロイ方式: GitHub Pages Actions

GitHub 側では、リポジトリの `Settings` -> `Pages` で `Build and deployment` の `Source` を `GitHub Actions` に設定してください。

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

## ライセンス

このリポジトリは MIT License で公開されています。詳細は [LICENSE](LICENSE) を参照してください。
