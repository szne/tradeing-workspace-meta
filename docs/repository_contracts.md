# Repository Contracts

## 1. 目的
マルチリポ構成における「責務境界」「依存方向」「変更責任」を明確化し、
機能追加時の衝突と互換性崩壊を防ぐ。

## 2. リポジトリ責務（Single Responsibility）

## 2.1 `trading-sdk`
- 戦略実装者と実行基盤の共通契約を定義する唯一のリポジトリ
- `BaseStrategy` / config schema / result schema の正本
- **禁止事項:** 実行制御・注文執行ロジックを持ち込まない

## 2.2 `trading-engine`
- 実行ライフサイクル（load -> validate -> run -> record）を担当
- DataLoader / Runner / Adapter / Safety Check を提供
- **禁止事項:** 戦略固有ロジックを内包しない

## 2.3 `strategy-pack`
- Strategy 実装とその検証コードを管理
- SDK契約準拠を担保し、Engine依存を最小化
- **禁止事項:** Engine内部APIへの直接依存（公開契約外）

## 2.4 `strategy-cli`
- Strategy 開発フロー（scaffold/validate/test/package）を統一
- Pack の品質チェックを自動化
- **禁止事項:** Engine内部構造を前提にした密結合

## 2.5 `workspace-meta`
- 全体方針、ロードマップ、互換・リリース規約の正本
- リポ横断で矛盾が出たときの裁定点
- **禁止事項:** 各リポの実装詳細を重複記述すること

## 3. 依存契約（Dependency Contract）

## 3.1 許可される依存方向
- `strategy-pack` -> `trading-sdk`
- `trading-engine` -> `trading-sdk`
- `strategy-cli` -> `trading-sdk`（必要に応じて `strategy-pack` を利用）
- `workspace-meta` はコード依存を持たない

## 3.2 禁止される依存
- `trading-sdk` -> 他リポ
- `strategy-pack` -> `trading-engine` の内部モジュール
- `trading-engine` -> `strategy-pack` の個別戦略モジュール

## 4. インターフェース契約（Interface Contract）

## 4.1 SDK公開契約
- Strategy の最小実装面は SDK が規定
- Engine/CLI/Pack は SDK の公開API以外に依存しない
- 非公開APIを使った場合は不具合時のサポート対象外

## 4.2 Engine Strategy ロード契約
- `strategy.class`: `package.module:ClassName` 形式
- `strategy.entrypoint`: `package.module:function_name` 形式
- 上記2方式は v2系で同等サポートを維持

## 4.3 CLI契約
- `validate`: SDK契約準拠の静的/動的検査
- `test`: 最小バックテストの実行可否確認
- CLIの出力フォーマット変更は MAJOR 判定対象

## 5. 変更責任（Change Ownership）

## 5.1 変更起点
- 契約変更がある場合は `trading-sdk` が起点
- 実行要件変更がある場合は `trading-engine` が起点
- 開発体験改善は `strategy-cli` が起点

## 5.2 反映順序
1. `workspace-meta`（方針・影響範囲）
2. `trading-sdk`
3. `trading-engine`
4. `strategy-pack`
5. `strategy-cli`

## 5.3 PR/レビュー観点（最低限）
- 契約互換（Backward Compatible か）
- 依存方向違反がないか
- ドキュメント更新漏れがないか
- 移行手順が利用者視点で再現可能か

## 5.4 Breaking Change 承認責任
- breaking change は `workspace-meta/docs/change_management.md` のBCRを必須とする
- 承認者は「変更対象リポMaintainer + 影響リポMaintainer」の2者を最低要件とする
- 承認前に実装マージしてはいけない

## 6. 受け入れ基準（Definition of Done）
- 影響する全リポで CI が緑
- `workspace-meta/docs/release_policy.md` と矛盾しない
- Engine で class/entrypoint 両方式のスモークが通る
- Strategy Pack で `validate` と最小 `test` が通る
- breaking change の場合、BCR と rollback plan/report の記録が存在する

## 7. 更新ルール
- 契約追加/変更時は本書を同一PRで更新する
- 変更履歴は末尾に日付付きで追記する

## 8. 更新履歴
- 2026-02-08: 初版作成
- 2026-02-09: M4.2対応（breaking change承認責任とrollback記録要件を追加）
