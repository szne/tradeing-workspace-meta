# Change Management (Breaking Change Approval Flow)

## 1. 目的
破壊的変更（breaking change）を「事前審査付きの計画変更」として扱い、
リポ間の互換崩壊と運用停止リスクを下げる。

## 2. 適用範囲
- `trading-sdk`
- `trading-engine`
- `strategy-pack`
- `strategy-cli`

`workspace-meta` 単体の文書変更は対象外。ただし運用ルール変更は本手順を準用する。

## 3. Breaking Change の判定
判定基準は `docs/release_policy.md` の SemVer 定義に従う。
以下のいずれかに該当したら breaking と判定する。

- 公開API/公開CLIの削除・意味変更
- 既存configで起動不能になる変更
- Strategy/Engine 間の契約（型/必須項目）破壊
- 出力フォーマット変更で既存自動化が壊れる変更

## 4. 必須成果物
breaking change は実装前に以下を揃える。

1. `docs/templates/breaking_change_request.md` で BCR（Breaking Change Request）を作成
2. 影響マトリクス（対象リポ・利用者影響・移行要否）
3. 移行ガイド（実行コマンド付き）
4. ロールバック計画（`docs/rollback_procedure.md` 準拠）

## 5. 承認フロー

### 5.1 承認者
最低2名の承認を必須とする。

- 変更対象リポの Maintainer 1名
- 影響を受ける別リポの Maintainer 1名

例: SDKの破壊的変更は `trading-sdk` + `trading-engine` の承認を必須化する。

### 5.2 承認ゲート
以下を満たした場合のみ `approved` に変更できる。

- 互換破壊の理由が明文化されている
- non-breaking 代替案の比較が記録されている
- 移行手順がローカルで再現確認済み
- ロールバック手順が実行可能な粒度で定義済み
- 影響リポのCIが緑（または対象ブランチで緑見込み）

## 6. 実行順序
1. `workspace-meta` に BCR と影響範囲を反映
2. 依存順（SDK -> Engine -> Pack -> CLI）で実装
3. 各リポでテスト/リリース
4. 互換マトリクスを更新

## 7. 例外（緊急対応）
重大障害時は PATCH 先行を許可するが、48時間以内に以下を追記する。

- 事後BCR
- 実施した暫定回避策
- 恒久対策と再発防止

## 8. ステータス定義
- `draft`: 作成中
- `review`: レビュー依頼中
- `approved`: 実装開始可能
- `released`: 本番反映済み
- `closed`: 後追い作業完了
