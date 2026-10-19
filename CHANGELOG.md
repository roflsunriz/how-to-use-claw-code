# 変更履歴

このプロジェクトの主な変更はこのファイルに記録します。

書式は [Keep a Changelog](https://keepachangelog.com/ja/1.1.0/) に基づきます。

## [Unreleased]

### Changed

- GitHub Actions の月間利用時間を抑えるため、Dependabot version update と自動マージを毎週・即時処理から毎月28日（Asia/Tokyo）の月末一括処理へ変更した。

- 利用者向けの手順と開発知見を区別できるように、固有の実現手段・共通指針の適用条件・調査記録をfindings.mdへ本文を保って分離し、作業前の全文必読と文書間の参照を整えた。

- 不具合・機能提案などの受付とPRの記入形式を揃え、プロジェクト固有の確認項目を残した。

- 英語版と日本語版のサイトを同じ外観で公開し続けるため、MkDocs Material から Zensical の classic テーマへ移行した。

- 依存更新を安全に省力化するため、Dependabot の patch／minor PR を既存 CI の全チェック成功後に自動取り込みし、失敗ジョブを一度再実行する設定を追加した。
- 作業開始時の共通指針見落としを防ぐため、調査やコマンド実行より前に `COMMON-AGENTS.md` を先頭から末尾まで読み、EOFを確認する必須ゲートを追加した。

### Fixed

- CI と Dependabot の分類の実行順が前後しても更新を取りこぼさないよう、同じ PR 番号と head SHA を再照合する経路を追加した。
