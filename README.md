# How to Use Claw Code

Claw CodeをWindowsで導入・利用するための手順をまとめたドキュメントリポジトリです。

このリポジトリでは、英語版と日本語版のWindows向け利用ガイドを管理し、MkDocsを使ってGitHub Pagesへ公開できるようにしています。

## ドキュメント

- [WindowsでClaw Codeを使う方法](docs/USAGE-ja-JP-win.md)
- [How to Use Claw Code on Windows](docs/USAGE-en-US-win.md)

GitHub Pagesで公開する場合は、MkDocsサイトのトップページからも各ドキュメントへ移動できます。

## ローカルでMkDocsサイトを確認する

Pythonが利用できる環境で、以下を実行します。

```powershell
python -m pip install -r requirements.txt
python -m mkdocs serve
```

ブラウザで以下を開くと、ローカルプレビューを確認できます。

```txt
http://127.0.0.1:8000/
```

静的サイトとしてビルドする場合は、以下を実行します。

```powershell
python -m mkdocs build --strict
```

ビルド結果は `site/` に出力されます。

## GitHub Pagesへのデプロイ

このリポジトリには、MkDocsサイトをGitHub PagesへデプロイするためのGitHub Actionsワークフローが含まれています。

- ワークフロー: `.github/workflows/deploy-mkdocs.yml`
- 対象ブランチ: `main`
- デプロイ方式: GitHub Pages Actions

GitHub側では、リポジトリの **Settings** → **Pages** で、**Build and deployment** の **Source** を **GitHub Actions** に設定してください。

## リポジトリ構成

```txt
.
├── docs/
│   ├── index.md
│   ├── USAGE-en-US-win.md
│   └── USAGE-ja-JP-win.md
├── .github/workflows/deploy-mkdocs.yml
├── mkdocs.yml
├── requirements.txt
├── README.md
└── LICENSE
```

## ライセンス

このリポジトリはMIT Licenseで公開されています。詳細は [LICENSE](LICENSE) を参照してください。
