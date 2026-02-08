# Master Roadmap

## 1. ロードマップの目的
複数リポジトリに分離した TradeingSystem を、
依存順と互換性を保ちながら段階的に進めるための全体計画を示す。

## 2. マイルストーン一覧

## M0: Foundation (完了)
- Core/Adapter/Runner/DataLoader の基本構築
- Backtest/Live の最小ループ
- 設定オーバーレイ・安全チェック・テスト基盤

### 完了条件
- Engine単体で backtest/live 最小実行が可能
- 主要コンポーネントの単体テスト整備

## M1: Strategy Externalization (完了)
- SDK/Pack/CLI を別リポ化
- Engine の `strategy.class` / `strategy.entrypoint` 対応
- workspace-meta 作成

### 完了条件
- class/entrypoint 両方で外部 Strategy 起動確認済み
- 各リポが独立 GitHub リポとして管理されている

## M2: Multi-Repo Hardening (進行中)
目的: マルチリポ運用の安定化

### M2.1 SDK Compatibility Policy
- [x] semver ルール確定（MAJOR/MINOR/PATCH）
- [x] 破壊的変更時の移行手順テンプレート
- [x] Engine の対応SDK範囲（例: `>=0.1,<0.2`）明記

### M2.2 Cross-Repo CI Baseline
- [x] Engine CI: `pytest` + class/entrypoint スモーク
- [x] SDK CI: lint + 契約互換チェック
- [x] Pack CI: `strategy validate` + `pytest`
- [x] CLI CI: unit test + basic command smoke

### M2.3 Release Workflow
- [ ] タグ運用（`vX.Y.Z`）統一
- [ ] リリースノート雛形統一
- [ ] リリース順序（SDK -> Engine -> Pack -> CLI）確定

### 完了条件
- 4リポでCIが常時緑
- semver と互換範囲が明文化される
- リリース作業が手順書どおりに再現可能

## M3: Validation & Optimization
目的: 実運用に向けた検証能力・性能の底上げ

### M3.1 Validation
- [ ] Walk-Forward Analysis
- [ ] パラメータ最適化（GA/Grid Search）
- [ ] Trainer向け `run_backtest()` API

### M3.2 Data/Perf
- [ ] Parquet/Arrow 対応
- [ ] ベクトル化/Numba
- [ ] 並列評価（銘柄/期間/候補）

### 完了条件
- 検証ループが自動化され、再現実験が可能
- バックテスト性能の改善が定量で示せる

## M4: Production Readiness
目的: ライブ運用の信頼性向上

### M4.1 Operations
- [ ] 障害時リカバリ手順
- [ ] 監視と通知（最小）
- [ ] API制限/接続断の運用設計

### M4.2 Governance
- [ ] 変更管理（breaking change承認フロー）
- [ ] ロールバック手順

### 完了条件
- 運用チェックリストで定常運用可能判定

## 3. 依存順実行ルール
実装は以下の順を守る。
1. `trading-sdk`
2. `trading-engine`
3. `strategy-pack`
4. `strategy-cli`
5. `workspace-meta`（全体反映）

## 4. 進捗管理ルール
- ステータス: `planned` / `active` / `done` / `blocked`
- 1マイルストーン = 複数の小さなコミット可
- ただしコミットは必ず「リポ単位」で分離
- マイルストーン完了時にこのファイルのチェックボックスを更新

## 5. 更新履歴
- 2026-02-08: 初版作成（M0/M1完了、M2開始）
- 2026-02-08: M2.1 完了（SDK互換ポリシー、移行テンプレ、SDK対応範囲）
- 2026-02-08: M2.2 完了（4リポにGitHub Actions CI基盤を導入）
