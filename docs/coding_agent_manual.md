# Coding Agent 運用マニュアル（ユーザー向け）

## 1. 目的
このマニュアルは、あなたがコーディングエージェントを使って
Strategy 開発を安全かつ高速に進めるための実運用手順を定義します。

## 2. 前提
- 開発対象リポ: `strategy-pack`
- 実行基盤: `trading-engine`
- 基本方針: **1戦略 = 1ファイル集中**

推奨編集対象:
- `strategy_pack/strategies/<strategy_name>.py`

## 3. 初回準備
```sh
cd /Users/szkk/works/TradeingSystem/trading-engine
source .venv/bin/activate
python -m pip install -e ../trading-sdk -e ../strategy-pack -e ../strategy-cli
```

データ保存先（ローカル外部化）:
```sh
export TRADING_ENGINE_DATA_PATH="/Users/szkk/works/TradeingSystem/trading-data/engine-cache"
```

## 4. エージェントへの依頼テンプレ

## 4.1 新規改善ループ開始
```text
strategy-pack の <strategy_name>.py だけ編集してください。
目的は「<例: 最大DDを下げる>」です。
他ファイルは触らず、validate/test/backtest まで実行し、
結果の差分を数値で報告してください。
```

## 4.2 条件追加（厳格版）
```text
必須制約:
- 編集は strategy_pack/strategies/<strategy_name>.py のみ
- trading-engine/core import 禁止
- 失敗時は原因と最小修正案を先に提示
```

## 5. 標準実行フロー
1. エージェントに改善目的を渡す
2. コード修正（1ファイル）
3. 検証コマンド実行
4. 結果を見て採用/却下を判断
5. 採用時のみコミット

検証コマンド:
```sh
cd /Users/szkk/works/TradeingSystem/strategy-pack
source ../trading-engine/.venv/bin/activate

strategy validate --path .
strategy test --path .
strategy backtest \
  --engine-root ../trading-engine \
  --config configs/default.yaml \
  --strategy strategy_pack.strategies.<strategy_name>:<ClassName> \
  --source synthetic \
  --rows 500
```

## 6. レビュー観点（採用判断）
- 契約違反がない（`validate` 通過）
- テストが通る
- 改善指標が目的に一致している
- ロジックが過剰に複雑化していない

## 7. やってはいけない運用
- 複数戦略を同時に編集する
- Engine内部にStrategyロジックを戻す
- 検証なしでコミットする
- いきなり live 常時実行する（最初は `--once`）

## 8. 失敗しにくい進め方
- 1回の変更は小さく（1〜2ロジック）
- 比較対象（前回統計）を必ず残す
- 連続3回失敗したら要件を分解して再定義する

## 9. 関連ドキュメント
- `workspace-meta/docs/user_manual.md`
- `strategy-pack/CODEX.md`
- `workspace-meta/docs/change_management.md`
- `workspace-meta/docs/rollback_procedure.md`
