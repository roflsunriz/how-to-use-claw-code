# 検証手順

## Zensical 文書ビルド（2026-09-27）

`python -m pip install -r requirements.txt` 後、`python -m zensical build --strict` を実行する。`site/index.html` と英語版・日本語版の代表ページ、ナビゲーション、検索を確認し、公開後は Pages の Actions と実サイトを確認する。

2026-09-27 のローカル検証では Zensical 0.0.65 の strict ビルドが成功し、Markdown 11 ページに対応する HTML 11 ページと 404 ページを生成した。英語版と日本語版の Mermaid コード出力を確認した。Pages 実配信は未検証。

## Dependabot 自動処理（2026-09-23）

詳細は [findings.md](findings.md#verification-1) に移しました。

## 2026-10-05: GitHub受付・READMEの整備（公開前）

- 比較元: `8b9f2880027f9842cd87d3253f48b888c3907a74`（`main`）。
- 受付フォーム 2 件のYAML構造、重複キー・ID、入力型、選択肢、予約ファイル名を一括検査し、エラー0件。
- 既存の固有質問・入力例・必須条件を原文と照合。READMEのリンク・画像・コマンド・条件を確認し、裏付けがある誤記だけを訂正した。
- 既存のCI、Dependabot、labeler、ライセンスのファイル内容は比較元から変更していない。
- 製品のビルド・インストール・実機操作、GitHub上のフォーム表示、公開後CIは今回の静的検証に含めない。公開後に実際の受付表示と必要ラベルの適用を確認する。

## 2026-10-16: findings.mdへの知見分離

- 目的: 共通指針に合わせ、AGENTSの行動指示と利用者向けREADMEから、開発内部の実現手段・例外の条件・再利用する調査知見をfindings.mdへ分離した。移動した本文の条件・確認日・未検証の留保を保持した。
- 元文書との本文照合、Markdownの相対リンク・見出しアンカーの検査、git diff --checkを実施し、新たなリンク不整合と空白エラーは0件。AGENTSの一般的な指示は内容を保って再編し、findings全文必読・禁止と承認条件を確認した。
- GitHubの既存Dependabot警告を読み取り、Open PRも確認した（作業開始時Open PR 0件、Open警告 0件）。GitHubの警告なしを全依存の安全保証や実機動作の検証とは扱わない。
- 追跡中のrequirementsファイルをpip-auditで検査し、いずれも既知の脆弱性0件だった。固定されたツールと依存範囲を変更していない。
- 既定PythonとPython 3.12にはZensicalが未導入だったため、作業用の隔離Python 3.12環境にrequirements.txtの固定版Zensical 0.0.69を導入し、strictビルドがNo issues foundで通過した。
- 文書移動と参照整理では製品の実行時ロジックを変更していない。実機書換え、認証操作、外部サービスへの投稿・署名・リリースは実施せず、これらの動作が再検証済みであるとは扱わない。
