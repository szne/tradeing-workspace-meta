# Project Charter

## 1. 目的
TradeingSystem を「戦略開発と実行基盤を分離したマルチリポ構成」として運用し、
開発速度・安全性・保守性を同時に高める。

## 2. スコープ
### In Scope
- `trading-engine`: 実行基盤（Runner/Adapter/DataLoader）
- `trading-sdk`: Strategy 契約（BaseStrategy/structs）
- `strategy-pack`: 戦略実装
- `strategy-cli`: 戦略開発支援CLI
- `workspace-meta`: 全体方針・ロードマップ・運用ルール

### Out of Scope
- HFT/超低遅延システム
- 単一リポへの回帰
- 運用監視基盤のフル実装（現時点）

## 3. 設計原則
1. **関心分離**: Strategy（思考）と Engine（執行・制御）を分離する。
2. **依存方向固定**: Strategy Pack -> SDK、Engine -> SDK。
3. **安全性優先**: 最終的な注文安全チェックは Adapter で実施。
4. **契約駆動**: `trading-sdk` を契約の唯一の基準とする。
5. **小さく進める**: マイルストーン単位で実装・検証・コミット。

## 4. 運用ガバナンス
### 4.1 ドキュメント優先順位
1. `workspace-meta/docs/project_charter.md`（本書）
2. `workspace-meta/docs/master_roadmap.md`
3. `workspace-meta/docs/repository_contracts.md`
4. 各リポの `README.md` / `docs/*`

### 4.2 変更フロー
1. 方針変更は `workspace-meta` を先に更新
2. 影響リポを依存順で更新（SDK -> Engine -> Pack -> CLI）
3. 各リポでテストを実行し、リポ単位でコミット

### 4.3 品質ゲート（最小）
- Engine: `pytest` + class/entrypointの疎通確認
- Strategy Pack: `strategy validate` + `pytest`
- Strategy CLI: `pytest` + CLIコマンド検証
- SDK: `ruff` + 型/契約の後方互換チェック

## 5. 成功条件（2026 v2系）
- 外部 Strategy の class/entrypoint ロードが安定稼働
- SDK の semver と互換ルールが固定
- 4リポで最低限のCIが継続運用
- 新規戦略を CLI + Pack のみで追加できる

## 6. 現在地（2026-02-08時点）
- v2.1（外部化）完了
  - SDK/Pack/CLI の分離
  - Engine 側動的ロード実装
  - workspace-meta 作成
- 次段階: v2.2（互換ポリシーとクロスリポCI強化）

## 7. リスクと対策
- **リスク:** SDK変更による破壊的影響
  - **対策:** semver厳守 + 互換期間を定義
- **リスク:** リポ間ドキュメント乖離
  - **対策:** workspace-meta を先に更新するルール
- **リスク:** Strategy品質のばらつき
  - **対策:** strategy-cli validate を必須化
