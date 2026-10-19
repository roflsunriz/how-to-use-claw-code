# 調査知見

- Zensicalは既存 `mkdocs.yml` とclassic themeを読む。日本語・英語の両方を `python -m zensical build --strict` で確認し、ローカル生成とPages配信結果を区別する。
- Dependabot分類がCIより遅れた場合は `callback_workflow_file` の呼出し側を `workflow_dispatch` し、現在のPR番号・head SHA・全チェックを再照合する。workflow名変更時はこの入力も更新する。
