# 更新手順

## 文書サイトの更新

`docs/` を編集したら、`python -m pip install -r requirements.txt` と `python -m zensical build --strict` を実行し、英語版と日本語版のページを確認する。Pages も同じビルドを使う。問題が出た場合は変更コミットを revert し、Pages ワークフローを再実行する。

## Dependabot PR の更新

詳細は [findings.md](findings.md#update-1) に移しました。
