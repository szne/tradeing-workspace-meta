# Rollback Procedure (M4.2)

## 1. 目的
リリース後に重大不具合が発生したとき、
安全に旧バージョンへ戻す標準手順を定義する。

## 2. ロールバック発動条件
以下のいずれかで発動する。

- 注文実行/資産保全に影響する不具合
- 複数リポに波及する互換崩壊
- 15分以内に暫定回避ができない障害
- 監視通知で連続失敗が閾値超過

## 3. ロールバック戦略

### 3.1 原則
- 変更を「取り消す」のではなく「安定版に戻す」
- リポ単位でコミットを分離する
- 依存関係を考慮して逆順で復旧する

### 3.2 基本順序
1. `strategy-cli`
2. `strategy-pack`
3. `trading-engine`
4. `trading-sdk`

> ただし障害起点が特定できる場合は、そのリポを最優先で戻す。

## 4. 実施手順

### Step 0: 影響固定
- 新規デプロイ/マージを停止
- 障害チャンネルに「rollback開始」を宣言

### Step 1: 対象バージョン特定
- 直近の安定タグ（`vX.Y.Z`）を確認
- 回帰した最小コミットを記録

### Step 2: 戻し方を選択
- 推奨: `git revert <sha>`（履歴を残す）
- 緊急: 安定タグへ hotfix ブランチを切って再リリース

### Step 3: 検証
- リポごとの最小テストを実行
- 互換スモーク（class/entrypoint、CLI validate/test）を確認

### Step 4: 再リリース
- `vX.Y.Z+1` のPATCHとして再発行
- リリースノートに rollback 理由と影響範囲を明記

### Step 5: 事後記録
- `docs/templates/rollback_report.md` でレポート作成
- `docs/master_roadmap.md` / 関連運用文書へ反映

## 5. 実行コマンド例

```sh
# 例: 問題コミットを取り消す
git checkout main
git pull origin main
git revert <bad_commit_sha>
python -m pytest
git commit -m "🐛 fix(rollback): revert unstable change"
git push origin main
```

```sh
# 例: 安定タグからhotfixを作る
git checkout -b hotfix/rollback-v0.1.3 v0.1.3
# 必要最小限の修正
python -m pytest
git commit -am "🐛 fix(hotfix): restore stable behavior"
git tag v0.1.4
git push origin hotfix/rollback-v0.1.3
git push origin v0.1.4
```

## 6. 完了判定
- 全対象リポでCIが緑
- 既知障害が再現しない
- rollback report が作成済み
- 次回防止策がオーナーと期限つきで登録済み
