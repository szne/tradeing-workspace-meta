# Release Workflow

## 1. 目的
4リポ構成（SDK / Engine / Pack / CLI）のリリース手順を標準化し、
再現可能な運用として固定する。

## 2. 対象リポジトリ
1. `trading-sdk`
2. `trading-engine`
3. `strategy-pack`
4. `strategy-cli`

> リリースは必ず上記順序で実施する。

## 3. タグ規約（統一）
- タグ形式は **`vX.Y.Z`** のみを許可する。
- 例: `v0.1.0`, `v0.1.1`, `v1.0.0`
- `release-0.1.0` や `0.1.0` は使用しない。

## 4. バージョン更新ルール
- MAJOR: 破壊的変更
- MINOR: 後方互換な機能追加
- PATCH: 後方互換な修正

バージョン更新時は `pyproject.toml` とリリースノートを同時更新する。

## 5. リリースノート雛形
- 標準テンプレート: `docs/templates/release_notes_template.md`
- 全リポで同じ見出し構成を使う。

## 6. 標準リリース手順（1リポあたり）
以下を対象リポのルートで実行する。

1. 最新取得
   - `git checkout main`
   - `git pull origin main`
2. 事前確認
   - 作業ツリーがクリーン
   - CI が green
3. バージョン更新
   - `pyproject.toml` の `version` を更新
4. リリースノート作成
   - `docs/templates/release_notes_template.md` を元に作成
5. 検証実行
   - 各リポのCI相当コマンドをローカルで実行
6. コミット
   - `git add ...`
   - `git commit -m "🔖 release: vX.Y.Z"`
7. タグ作成
   - `git tag vX.Y.Z`
8. push
   - `git push origin main`
   - `git push origin vX.Y.Z`
9. GitHub Release 作成
   - タグ `vX.Y.Z` を選択し、リリースノートを貼り付け

## 7. クロスリポ実行順序（1リリースサイクル）
1. SDKをリリース
2. EngineをSDK互換範囲で更新してリリース
3. Packをリリース
4. CLIをリリース
5. 最後に `workspace-meta` の互換マトリクスを更新

## 8. 完了条件（M2.3）
- タグ規約が `vX.Y.Z` に統一されている
- リリースノート雛形が共通化されている
- SDK -> Engine -> Pack -> CLI の順序が運用手順に固定されている

## 9. 更新履歴
- 2026-02-08: 初版作成（M2.3）
