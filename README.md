# TradeingSystem Workspace

このディレクトリはマルチリポ構成のワークスペースです。

## Repositories
- `trading-engine/` : 実行基盤（Runner/Adapter/DataLoader）
- `trading-sdk/` : Strategy契約（BaseStrategy/structs）
- `strategy-pack/` : 外部Strategy実装
- `strategy-cli/` : Strategy開発支援CLI

## 共通ドキュメント
- `docs/workspace_guide.md` : 全リポ共通の運用ルール

## 依存方向
`strategy-pack` -> `trading-sdk`
`trading-engine` -> `trading-sdk`
`strategy-cli` -> （必要に応じて）`strategy-pack` / `trading-engine` を呼び出し
