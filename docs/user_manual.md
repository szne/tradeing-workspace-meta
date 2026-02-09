# User Manual (TradeingSystem v2)

## 1. このマニュアルの目的
このドキュメントは、TradeingSystem v2 の全体像を短時間で把握し、
日常運用（Strategy作成 -> 検証 -> ライブ試験 -> リリース）を迷わず進めるための利用者向け手引きです。

## 2. まず全体像（3分）

### 2.1 何がどこにあるか
- `trading-sdk`: Strategy と Engine の共通契約（型/基底クラス）
- `strategy-pack`: 実際の戦略実装
- `strategy-cli`: Strategy 開発支援コマンド
- `trading-engine`: 実行基盤（backtest/live/trainers）
- `workspace-meta`: 全体方針・規約・ロードマップ

### 2.2 依存方向
- `strategy-pack` -> `trading-sdk`
- `trading-engine` -> `trading-sdk`
- `strategy-cli` -> `trading-sdk`（必要に応じて Pack/Engine を利用）

## 3. 最初のセットアップ

ワークスペースルート（`/Users/szkk/works/TradeingSystem`）で実施します。

```sh
cd /Users/szkk/works/TradeingSystem/trading-engine
python3.11 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
python -m pip install -r requirements-dev.txt
python -m pip install -e ../trading-sdk -e ../strategy-pack -e ../strategy-cli
```

補足:
- Parquet を使う場合は `pyarrow` が必要です（`requirements*.txt` に含まれる想定）。
- 機密情報は `.env` に置きます（例: `OPENAI_API_KEY`, `LIVE_ALERT_WEBHOOK_URL`）。

## 4. 日常フロー（最重要）

## 4.1 新しいStrategyを作る

```sh
cd /Users/szkk/works/TradeingSystem/strategy-pack
source ../trading-engine/.venv/bin/activate
strategy new my_strategy --path .
```

次に `pyproject.toml` の entrypoint group
`trading_system.strategies` に `my_strategy` を登録します。

## 4.2 Strategy品質を検証する

```sh
cd /Users/szkk/works/TradeingSystem/strategy-pack
source ../trading-engine/.venv/bin/activate
strategy validate --path .
strategy test --path .
```

## 4.3 Engineでバックテストする

```sh
cd /Users/szkk/works/TradeingSystem/strategy-pack
source ../trading-engine/.venv/bin/activate
strategy backtest --engine-root ../trading-engine --config configs/default.yaml --strategy strategy_pack.strategies.my_strategy:MyStrategy --source synthetic --rows 500
```

または Engine 側で直接実行:

```sh
cd /Users/szkk/works/TradeingSystem/trading-engine
source .venv/bin/activate
python -m runners.backtest_runner configs/default.yaml --strategy strategy_pack.strategies.my_strategy:MyStrategy --source synthetic --rows 500
```

## 4.4 ライブ試験を最小構成で実行する

```sh
cd /Users/szkk/works/TradeingSystem/trading-engine
source .venv/bin/activate
python -m runners.live_runner configs/default.yaml --once
```

`--once` は安全確認用です。常時ループにする前に必ず1回通します。

## 5. データ管理の考え方

- データ管理の責務は Engine 側（`trading-engine/data/`）
- 原則: データは Git にコミットしない
- 対応ソース: `auto/local_csv/local_parquet/http_csv/synthetic`
- キャッシュ命名は `docs/data_sources.md` の規約に従う

Parquet生成例:

```sh
cd /Users/szkk/works/TradeingSystem/trading-engine
source .venv/bin/activate
python scripts/fetch_sample_data.py --source synthetic --symbol BTC/USDT --timeframe 1m --rows 10000 --format parquet
```

## 6. 運用・リリース

## 6.1 リリース順序（固定）
1. `trading-sdk`
2. `trading-engine`
3. `strategy-pack`
4. `strategy-cli`

詳細は `workspace-meta/docs/release_workflow.md` を参照。

## 6.2 breaking change の扱い
- 実装前に BCR を作成する
- 2者承認（対象リポ Maintainer + 影響リポ Maintainer）を取る
- ロールバック計画を先に定義する

参照:
- `workspace-meta/docs/change_management.md`
- `workspace-meta/docs/templates/breaking_change_request.md`

## 6.3 障害時
- まず `workspace-meta/docs/rollback_procedure.md` を起動
- ロールバック後に `rollback_report.md` を作成

## 7. 迷ったときの参照順
1. `workspace-meta/docs/project_charter.md`
2. `workspace-meta/docs/master_roadmap.md`
3. `workspace-meta/docs/repository_contracts.md`
4. 各リポの README / docs

## 8. よくある詰まりポイント

- `strategy validate` が落ちる
  - `ccxt` や `core.*` を Strategy で import していないか確認
- entrypoint が解決できない
  - `pyproject.toml` の group が `trading_system.strategies` か確認
- Parquet が読めない
  - `pyarrow` が入っているか確認
- live で通知が飛ばない
  - `LIVE_ALERT_WEBHOOK_URL` と `notifications.webhook_url_env` を確認

## 9. 現在の到達点
- Roadmap の M0〜M4.2 はチェックボックス上すべて完了
- 現在は「運用定着フェーズ」（手順を回して改善する段階）
