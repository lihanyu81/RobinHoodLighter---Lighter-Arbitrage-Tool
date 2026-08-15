# RH Lighter 与 Lighter 交易对映射表

本表用于记录 RH Lighter（RBLighter）与 Lighter 之间可进行价差套利的同名永续合约映射关系。

数据来源：两边的 `GET /api/v1/orderBookDetails` 接口。

核对日期：2026-08-16。

本次查询结果：Lighter 有 210 个活跃永续市场，RH Lighter 有 40 个活跃永续市场，按交易标的交集得到 36 个映射。

| 标的 | Lighter Symbol | Lighter Market Index | RH Lighter Symbol | RH Lighter Market Index |
|---|---|---:|---|---:|
| AAPL | AAPL | 113 | AAPL | 10 |
| AMD | AMD | 138 | AMD | 29 |
| AMZN | AMZN | 114 | AMZN | 11 |
| ANSEM | ANSEM | 219 | ANSEM | 39 |
| ANTHROPIC | ANTHROPIC | 193 | ANTHROPIC | 38 |
| BABA | BABA | 177 | BABA | 19 |
| BE | BE | 196 | BE | 20 |
| BTC | BTC | 1 | BTC | 1 |
| CASHCAT | CASHCAT | 221 | CASHCAT | 36 |
| COIN | COIN | 109 | COIN | 23 |
| CRCL | CRCL | 121 | CRCL | 24 |
| CRWV | CRWV | 167 | CRWV | 33 |
| ETH | ETH | 0 | ETH | 0 |
| GOOGL | GOOGL | 116 | GOOGL | 12 |
| HYPE | HYPE | 24 | HYPE | 2 |
| INTC | INTC | 137 | INTC | 30 |
| LIT | LIT | 120 | LIT | 5 |
| META | META | 117 | META | 13 |
| MSFT | MSFT | 115 | MSFT | 14 |
| MU | MU | 164 | MU | 31 |
| NEAR | NEAR | 10 | NEAR | 7 |
| NVDA | NVDA | 110 | NVDA | 15 |
| ORCL | ORCL | 165 | ORCL | 17 |
| PLTR | PLTR | 111 | PLTR | 34 |
| QQQ | QQQ | 129 | QQQ | 25 |
| SKHY | SKHY | 216 | SKHY | 37 |
| SNDK | SNDK | 139 | SNDK | 32 |
| SOL | SOL | 2 | SOL | 3 |
| SOXL | SOXL | 197 | SOXL | 35 |
| SPCX | SPCX | 194 | SPCX | 18 |
| SPY | SPY | 128 | SPY | 26 |
| SUI | SUI | 16 | SUI | 9 |
| TSLA | TSLA | 112 | TSLA | 16 |
| VVV | VVV | 69 | VVV | 8 |
| XRP | XRP | 7 | XRP | 6 |
| ZEC | ZEC | 90 | ZEC | 4 |

## 使用说明

1. `Lighter Market Index` 只能用于 Lighter。
2. `RH Lighter Market Index` 只能用于 RH Lighter。
3. 即使两个交易所的 Symbol 相同，也不能复用对方的 Market Index、账户 Index 或 API Key。
4. 下单数量精度、价格精度、最小下单量和 tick size 也必须分别使用对应交易所的市场元数据。
5. 市场会新增、下线或调整参数，表格不能替代启动前的动态核验。

## 动态核验

源码环境中可以运行：

```bash
PYTHONPATH=src python -m arbitrage.verify
```

程序会重新读取两边的 `orderBookDetails`，只保留 `market_type=perp` 且 `status=active` 的同名交易标的，并输出两边的 Market Index、数量精度、价格精度、最小数量和 tick size。
