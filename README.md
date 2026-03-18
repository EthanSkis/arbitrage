# ArbEdge — BTC 15-Min Arbitrage | Polymarket vs Kalshi

<p align="center">
  <img src="https://img.shields.io/badge/version-4.0-green?style=flat-square" alt="Version">
  <img src="https://img.shields.io/badge/asset-Bitcoin-orange?style=flat-square" alt="Asset">
  <img src="https://img.shields.io/badge/interval-15--minute-blue?style=flat-square" alt="Interval">
  <img src="https://img.shields.io/badge/trading-simulated-yellow?style=flat-square" alt="Trading Mode">
  <img src="https://img.shields.io/badge/data-live-brightgreen?style=flat-square" alt="Data Source">
  <img src="https://img.shields.io/badge/license-MIT-gray?style=flat-square" alt="License">
</p>

**ArbEdge** is a real-time Bitcoin 15-minute up/down arbitrage scanner and paper trading simulator. It fetches **live market data** from both [Polymarket](https://polymarket.com) and [Kalshi](https://kalshi.com), matches identical BTC 15-minute prediction windows across platforms by timestamp, and runs a realistic simulated trading engine with real-world cost modeling.

> **Disclaimer:** This tool is for **educational and research purposes only**. It performs **simulated (paper) trading** — no real orders are placed. Prediction market trading involves risk. Always do your own research and comply with applicable regulations.

---

## What It Does

Both Polymarket and Kalshi offer **"Bitcoin Up or Down"** prediction markets that resolve every 15 minutes. ArbEdge finds cases where the "Up" price differs between the two platforms for the **same time window** — that price difference is an arbitrage opportunity.

**Example:** If Polymarket prices BTC Up at 48¢ and Kalshi prices BTC Up at 53¢ for the same 15-minute window, you could buy on Poly and sell on Kalshi to lock in a 5¢/contract spread.

---

## Features

### BTC 15-Min Market Focus
- **Polymarket** — Fetches `btc-updown-15m-*` markets (powered by Chainlink BTC/USD oracles)
- **Kalshi** — Fetches `KXBTC15M` series markets (powered by CF Benchmarks RTI)
- **Time-Window Matching** — Pairs markets by their 15-minute window timestamps, not fuzzy keyword matching
- **No False Matches** — Only pairs markets that cover the exact same 15-minute period

### Realistic Trading Simulator
- **Hold-to-Resolution** — Positions are held until the 15-minute window ends (the real arb strategy)
- **Platform Fees** — Polymarket ~2% taker + gas, Kalshi 7% profit fee + 1¢/contract
- **Bid-Ask Slippage** — 0.5% average spread crossing + market impact scaling with order size
- **Partial Fills** — 75-95% fill rate simulation (real order books have limited depth)
- **Annualized ROI** — Shows what your return would be if you repeated the same trade all year

### Dashboard Panels

| Panel | Description |
|-------|-------------|
| **BTC 15-Min Scanner** | Live table of matched time windows with spread, direction, and status |
| **Order Book** | Side-by-side Polymarket vs Kalshi order book for selected window |
| **Performance** | Realized P&L, locked profit (pending resolution), net after fees, Sharpe ratio, equity curve |
| **Open Positions** | Active trades with countdown to resolution, locked profit, and annualized ROI |
| **Spread History** | Autoscaling bar chart of top spread over time with threshold line |
| **Trade Log** | Full execution history with BUY/SETTLE entries, fees, and P&L |
| **Risk Management** | Capital locked, max drawdown, fees paid, slippage cost, avg hold time, annualized ROI |
| **Event Feed** | Color-coded alerts for trades, skipped opportunities, and API events |

### Charts
- **Autoscaling** — Both equity and spread charts dynamically scale Y-axis to data range
- **Y-Axis Labels** — Gridlines with dollar/percentage labels that update as data changes

### Technical Details
- **Zero Dependencies** — Single self-contained HTML file, no build step, no npm, no frameworks
- **Pure Canvas Charts** — Equity curve and spread history rendered with the Canvas API
- **Responsive Design** — Full viewport layout that fits on any screen without scrolling
- **Dark Theme** — Designed for extended monitoring sessions

---

## Quick Start

### Option 1: Open Directly
```bash
git clone https://github.com/EthanSkis/arbitrage.git
cd arbitrage
open index.html        # macOS
xdg-open index.html    # Linux
start index.html       # Windows
```

### Option 2: Local Server (Recommended for API calls)
```bash
python3 -m http.server 8080
# Then navigate to http://localhost:8080
```

### Option 3: GitHub Pages
Deploy as a static site — enable GitHub Pages pointing to the `main` branch.

---

## Configuration

| Setting | Default | Description |
|---------|---------|-------------|
| **Bot Toggle** | ON | Enable/disable auto-trading |
| **Refresh Rate** | 15s | How often to poll APIs for new BTC 15-min markets |
| **Min Spread** | 2% | Minimum spread to trigger an arbitrage trade |
| **Max Position** | $500 | Maximum dollar size per position |
| **Risk Mode** | Moderate | Position size multiplier (0.5x / 1x / 1.5x) |
| **Strategy** | Cross-Mkt | Hold-to-resolution (default) or Scalp (early exit) |

---

## API Endpoints

### Polymarket
| Endpoint | Purpose |
|----------|---------|
| `GET gamma-api.polymarket.com/markets?slug_contains=btc-updown-15m` | BTC 15-min up/down markets |
| `GET clob.polymarket.com/book?token_id=X` | Real-time order book |

### Kalshi
| Endpoint | Purpose |
|----------|---------|
| `GET api.elections.kalshi.com/trade-api/v2/events?series_ticker=KXBTC15M` | BTC 15-min up/down events |
| `GET api.elections.kalshi.com/trade-api/v2/markets/{ticker}/orderbook` | Order book depth |

> **Note:** No API keys required for reading public market data.

---

## How It Works

1. **Fetch** — Pull active BTC 15-min up/down markets from both platforms
2. **Extract Time Windows** — Parse the 15-minute window start time from Polymarket slugs and Kalshi close times
3. **Match by Timestamp** — Pair markets that cover the same 15-minute window (within 2-minute tolerance)
4. **Calculate Spread** — Compare "Up" prices between matched pairs
5. **Apply Costs** — Subtract slippage, platform fees, and market impact
6. **Execute** — If net spread is positive and exceeds threshold, enter the arb
7. **Resolve** — Hold until the 15-minute window ends, collect guaranteed profit

---

## Real-World Cost Model

| Cost | Amount | Notes |
|------|--------|-------|
| Polymarket taker fee | 2% | On winnings |
| Polymarket gas | ~$0.50 | Per trade (Polygon network) |
| Kalshi contract fee | 1¢/contract | Per contract |
| Kalshi profit fee | 7% | On profit |
| Bid-ask slippage | ~0.5% | Crossing the spread |
| Market impact | ~0.2% | Scales with order size |
| Partial fills | 75-95% | Real order book depth limits |

---

## Limitations

- **CORS** — Browser security may block direct API calls. Falls back to CORS proxies automatically.
- **Rate Limits** — Both APIs have rate limits. The configurable refresh rate helps stay within bounds.
- **Market Availability** — BTC 15-min markets roll every 15 minutes. Some windows may not have active markets on both platforms simultaneously.
- **Simulated Only** — No real orders are placed. Real execution may differ from simulation.
- **Resolution Source Difference** — Polymarket uses Chainlink BTC/USD; Kalshi uses CF Benchmarks RTI. Slight price feed differences could affect resolution.

---

## License

MIT License — see [LICENSE](LICENSE) for details.

---

## Disclaimer

This software is provided "as is" for educational and research purposes. It does **not** constitute financial advice. The authors are not responsible for any financial losses incurred through the use of this tool. Prediction market trading carries inherent risks. Always trade responsibly and within your means.
