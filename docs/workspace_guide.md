# Workspace Guide

## 目的
- リポジトリを分離しつつ、1つのワークスペースで運用する。
- 共通ルールはこのファイルで管理し、各リポの詳細仕様は各README/docsで管理する。

## 共通ルール
- 変更はリポ単位でコミットする。
- 依存順で更新する（SDK -> Engine -> Strategy Pack -> CLI）。
- 破壊的変更はSDK側で先に宣言し、移行手順を同時に記載する。
- リリース時は `docs/release_workflow.md` に従い、`vX.Y.Z` タグのみを使う。

## 連携確認の最小手順
1. `trading-sdk` をeditable install
2. `strategy-pack` をeditable install
3. `trading-engine` で `strategy.class` / `strategy.entrypoint` の両方を実行確認
4. `strategy-cli` の `validate` と `test` を実行
