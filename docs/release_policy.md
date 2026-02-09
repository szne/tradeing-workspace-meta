# Release Policy

## 1. 目的
マルチリポ環境での互換性崩壊を防ぎ、
リリースを再現可能な手順として定義する。

## 2. 対象リポジトリ
- `trading-sdk`
- `trading-engine`
- `strategy-pack`
- `strategy-cli`

## 3. バージョニング規約（SemVer）

## 3.1 `trading-sdk`（最重要契約）
- **MAJOR**: 既存 Strategy/Engine を壊す変更
  - 例: 型削除、必須引数変更、意味変更
- **MINOR**: 後方互換な機能追加
  - 例: フィールド追加（デフォルトあり）、API追加
- **PATCH**: 後方互換な修正のみ

### SDK互換原則
- 少なくとも1つ前の MINOR 系は互換維持を目標とする
- MAJOR 変更時は移行ガイド必須

## 3.2 `trading-engine`
- **MAJOR**: config互換を壊す変更、実行契約変更
- **MINOR**: 新機能追加（互換維持）
- **PATCH**: バグ修正/非互換なし

## 3.3 `strategy-pack`
- **MAJOR**: 公開Strategyの振る舞い仕様を壊す変更
- **MINOR**: 新戦略追加・既存改善（互換維持）
- **PATCH**: バグ修正

## 3.4 `strategy-cli`
- **MAJOR**: コマンド仕様の非互換変更
- **MINOR**: サブコマンド追加、互換な機能追加
- **PATCH**: 修正のみ

## 4. リリース順序
必ず以下の順で行う。
1. `trading-sdk`
2. `trading-engine`
3. `strategy-pack`
4. `strategy-cli`

理由: 依存方向に逆らうと互換性が検証不能になるため。

## 5. リリース前チェック

## 5.1 共通
- 作業ツリーがクリーン
- バージョン更新済み（`pyproject.toml`）
- CHANGELOG/リリースノート更新

## 5.2 リポ別
- SDK: lint + 型/契約互換確認
- Engine: `pytest` + class/entrypoint 起動確認
- Pack: `strategy validate` + `pytest`
- CLI: `pytest` + 主要コマンド smoke

## 6. リリース手順（標準）
1. `main` を最新化
2. バージョン更新
3. テスト実行
4. コミット
5. タグ作成 (`vX.Y.Z`)
6. push（branch + tag）
7. GitHub Release 作成

詳細な実行手順は `docs/release_workflow.md` を参照する。
リリースノートは `docs/templates/release_notes_template.md` を使用する。

## 7. 破壊的変更（Breaking Change）手順
1. `docs/change_management.md` に従い BCR を作成する
2. `workspace-meta` に影響範囲を先に記載する
3. SDK/Engine の移行ガイドを同時リリースする
4. 少なくとも1リリースは旧方式への注意喚起を残す
5. `docs/rollback_procedure.md` 準拠のロールバック計画を事前定義する
6. 対象リポMaintainer + 影響リポMaintainer の2者承認を取得する

## 8. 互換マトリクス（初期方針）

| Engine | SDK | Pack | CLI |
|---|---|---|---|
| 0.1.x | >=0.1,<0.2 | 0.1.x | 0.1.x |

> 互換マトリクスはリリース時に更新する。

## 9. 例外運用
緊急修正時のみ以下を許可:
- PATCH を先行リリース
- 後続でドキュメント追補

ただし 48時間以内に正式な記録を `workspace-meta` に反映する。

## 10. 関連ドキュメント
- `docs/change_management.md`
- `docs/rollback_procedure.md`
- `docs/templates/breaking_change_request.md`
- `docs/templates/rollback_report.md`

## 11. 更新履歴
- 2026-02-08: 初版作成
- 2026-02-09: M4.2対応（breaking change承認フローとrollback手順を統合）
