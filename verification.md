# 検証手順

## Zensical 文書ビルド（2026-09-27）

`python -m pip install -r requirements.txt` 後、`python -m zensical build --strict` を実行する。`site/index.html` と英語版・日本語版の代表ページ、ナビゲーション、検索を確認し、公開後は Pages の Actions と実サイトを確認する。

2026-09-27 のローカル検証では Zensical 0.0.65 の strict ビルドが成功し、Markdown 11 ページに対応する HTML 11 ページと 404 ページを生成した。英語版と日本語版の Mermaid コード出力を確認した。Pages 実配信は未検証。

## Dependabot 自動処理（2026-09-23）

`.github/workflows/dependabot-automation.yml` を actionlint で検査し、PR 用 workflow 名（CI）と一致することを確認する。Dependabot の patch／minor かつ全 PR チェック成功の場合だけ取り込み、major・古い SHA・再失敗は残す。

実際の Dependabot PR がまだない場合、動作経路は未検証として扱う。実 PR 発生後に自動化ジョブ、CI の再試行、マージ結果を確認する。

大量の Dependabot PR により CI 完了より分類が遅れる場合でも、分類後の `workflow_dispatch` が現在の PR 番号と head SHA を照合して再評価する。別の作成者、古い SHA、未完了の CI はマージしない。
