# TradeingSystem Workspace

このディレクトリはマルチリポ構成のワークスペースです。

## Repositories
- `trading-engine/` : 実行基盤（Runner/Adapter/DataLoader）
- `trading-sdk/` : Strategy契約（BaseStrategy/structs）
- `strategy-pack/` : 外部Strategy実装
- `strategy-cli/` : Strategy開発支援CLI

## 共通ドキュメント
- `docs/user_manual.md` : 利用者向けマニュアル（全体像と日常フロー）
- `docs/workspace_guide.md` : 全リポ共通の運用ルール
- `docs/project_charter.md` : 全体方針・設計原則・ガバナンス
- `docs/master_roadmap.md` : 全リポ横断ロードマップ
- `docs/repository_contracts.md` : リポ責務/依存/契約定義
- `docs/release_policy.md` : バージョニング/互換/リリース規約
- `docs/release_workflow.md` : リリース実行手順（順序/タグ/作業手順）
- `docs/change_management.md` : breaking change の承認フロー
- `docs/rollback_procedure.md` : 障害時ロールバック標準手順
- `docs/templates/release_notes_template.md` : 共通リリースノート雛形
- `docs/templates/breaking_change_request.md` : BCR雛形
- `docs/templates/rollback_report.md` : rollback報告雛形

## 依存方向
`strategy-pack` -> `trading-sdk`
`trading-engine` -> `trading-sdk`
`strategy-cli` -> （必要に応じて）`strategy-pack` / `trading-engine` を呼び出し
