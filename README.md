# ArbEdge — Polymarket & Kalshi Arbitrage Dashboard

<p align="center">
  <img src="https://img.shields.io/badge/version-3.0-green?style=flat-square" alt="Version">
  <img src="https://img.shields.io/badge/markets-Polymarket%20%7C%20Kalshi-blue?style=flat-square" alt="Markets">
  <img src="https://img.shields.io/badge/trading-simulated-yellow?style=flat-square" alt="Trading Mode">
  <img src="https://img.shields.io/badge/data-live-brightgreen?style=flat-square" alt="Data Source">
  <img src="https://img.shields.io/badge/license-MIT-gray?style=flat-square" alt="License">
</p>

**ArbEdge** is a real-time cross-platform prediction market arbitrage scanner and paper trading simulator. It fetches **live market data** from [Polymarket](https://polymarket.com) and [Kalshi](https://kalshi.com), automatically detects arbitrage opportunities by matching equivalent markets across both platforms, and runs a configurable simulated trading engine on real prices.

> **Disclaimer:** This tool is for **educational and research purposes only**. It performs **simulated (paper) trading** — no real orders are placed. Prediction market trading involves risk. Always do your own research and comply with applicable regulations.

---

## Features

### Live Market Data
- **Polymarket Integration** — Fetches active markets from the [Gamma API](https://gamma-api.polymarket.com) and real-time order books from the [CLOB API](https://clob.polymarket.com)
- **Kalshi Integration** — Fetches open markets and order books from the [Kalshi Trade API](https://api.elections.kalshi.com/trade-api/v2)
- **Auto CORS Proxy** — Falls back to a CORS proxy automatically if direct API calls are blocked by the browser
- **Configurable Refresh Rate** — 5s / 10s / 15s / 30s polling intervals

### Cross-Platform Arbitrage Detection
- **Keyword Matching Engine** — Automatically matches equivalent markets across Polymarket and Kalshi using NLP-based keyword extraction and fuzzy scoring
- **Spread Calculation** — Computes the YES price differential between matched pairs and identifies the optimal direction (Buy Poly / Sell Kalshi or vice versa)
- **Multi-Leg Detection** — Identifies hedge opportunities where buying YES on one platform + NO on the other guarantees profit
- **Match Scoring** — Ranks pairs by match confidence and spread size

### Paper Trading Simulator
- **Auto-Trading Bot** — Configurable bot that automatically enters arbitrage positions when spread exceeds your threshold
- **Position Management** — Automatic exit on spread collapse, reversal, or timeout
- **Three Strategies:**
  - **Cross-Market** — Standard cross-platform spread capture
  - **Hedged** — Balanced positions across both platforms
  - **Scalp** — Quick in-and-out on temporary spread spikes
- **Risk Modes:** Conservative (0.5x), Moderate (1x), Aggressive (1.5x) position sizing

### Dashboard Panels (3x3 Grid — No Scrolling)

| Panel | Description |
|-------|-------------|
| **Arbitrage Scanner** | Live table of all markets — matched pairs ranked by spread, plus unmatched singles |
| **Order Book** | Side-by-side Polymarket vs Kalshi order book for selected market (real data when available) |
| **Performance** | Realized/unrealized P&L, win rate, avg profit per trade, Sharpe ratio, equity curve chart |
| **Open Positions** | Active paper trades with live P&L |
| **Spread History** | Bar chart of top opportunity spread over time with min-spread threshold line |
| **Trade Log** | Full execution history with timestamps, platform, price, and P&L |
| **Risk Management** | Exposure, max drawdown, VaR (95%), correlation risk, margin usage, hedge ratio |
| **Event Feed** | Color-coded alerts for trades, API events, and system warnings |

### Technical Details
- **Zero Dependencies** — Single self-contained HTML file, no build step, no npm, no frameworks
- **Pure Canvas Charts** — Equity curve and spread history rendered with the Canvas API
- **Responsive Design** — Full viewport layout that fits on any screen without scrolling
- **Dark Theme** — Designed for extended monitoring sessions

---

## Quick Start

### Option 1: Open Directly
```bash
# Clone the repo
git clone https://github.com/EthanSkis/arbitrage.git
cd arbitrage

# Open in your browser
open index.html        # macOS
xdg-open index.html    # Linux
start index.html       # Windows
```

### Option 2: Local Server (Recommended for API calls)
```bash
# Python
python3 -m http.server 8080

# Node.js
npx serve .

# Then navigate to http://localhost:8080
```

### Option 3: GitHub Pages
The dashboard is deployable as a static site on GitHub Pages — just enable it in your repo settings pointing to the `main` branch.

---

## Configuration

All settings are configurable via the control bar at the top of the dashboard:

| Setting | Default | Description |
|---------|---------|-------------|
| **Bot Toggle** | ON | Enable/disable auto-trading |
| **Refresh Rate** | 15s | How often to poll APIs for new data |
| **Min Spread** | 2% | Minimum spread to trigger an arbitrage trade |
| **Max Position** | $500 | Maximum dollar size per position |
| **Risk Mode** | Moderate | Position size multiplier (0.5x / 1x / 1.5x) |
| **Strategy** | Cross-Mkt | Trading strategy selection |

---

## API Endpoints Used

### Polymarket (No Authentication Required)
| Endpoint | Purpose |
|----------|---------|
| `GET gamma-api.polymarket.com/markets` | Fetch active markets with prices and volume |
| `GET clob.polymarket.com/book?token_id=X` | Real-time order book for a specific market |
| `GET clob.polymarket.com/price?token_id=X&side=BUY` | Best bid price |

### Kalshi (No Authentication Required)
| Endpoint | Purpose |
|----------|---------|
| `GET api.elections.kalshi.com/trade-api/v2/markets` | Fetch open markets with yes/no prices |
| `GET api.elections.kalshi.com/trade-api/v2/markets/{ticker}/orderbook` | Order book depth |

> **Note:** API keys are **not required** for reading public market data. Keys are only needed for placing real orders (which this tool does not do).

---

## Architecture

```
index.html (single file)
├── CSS — Full dashboard styling, dark theme, responsive 3x3 grid
├── HTML — 9-panel dashboard layout, header, controls, status bar
└── JavaScript
    ├── API Layer — Fetch, parse, and normalize data from both platforms
    ├── Matching Engine — Keyword extraction + fuzzy scoring to pair markets
    ├── Trading Engine — Paper trading simulator with auto-entry/exit logic
    ├── Risk Engine — Exposure, drawdown, VaR, correlation tracking
    ├── Chart Engine — Canvas-based equity curve and spread history
    └── UI Renderer — Real-time DOM updates for all 9 panels
```

---

## How Arbitrage Detection Works

1. **Fetch** — Pull active/open markets from both Polymarket and Kalshi APIs
2. **Normalize** — Extract market titles, YES prices, and volume from each platform's response format
3. **Match** — For each Polymarket market, find the Kalshi market with the highest keyword overlap score (threshold: 25%+)
4. **Calculate** — Compute the spread between matched pairs' YES prices
5. **Rank** — Sort by spread descending; matched pairs appear first
6. **Execute** — If bot is active and spread exceeds threshold, open a simulated position
7. **Monitor** — Close positions when spread collapses, reverses, or times out

---

## Limitations

- **CORS** — Browser security may block direct API calls. The dashboard automatically falls back to a CORS proxy (`corsproxy.io`). For best results, run a local server.
- **Rate Limits** — Both APIs have rate limits. The configurable refresh rate helps stay within bounds.
- **Market Matching** — Keyword-based matching is heuristic. Some pairs may be false positives or missed entirely. Always verify matches visually.
- **Simulated Only** — No real orders are placed. Simulated fills assume perfect execution which may not reflect real market conditions (slippage, fees, liquidity).
- **Static Prices Between Refreshes** — Prices update on each API poll, not in real-time via WebSocket.

---

## Roadmap

- [ ] WebSocket integration for real-time Polymarket price streaming
- [ ] Kalshi WebSocket support (`wss://api.kalshi.com/trade-api/ws/v2`)
- [ ] Improved market matching using event-level grouping
- [ ] Historical spread tracking and backtesting
- [ ] Configurable alerts (email, webhook, sound)
- [ ] Multi-leg arbitrage calculator
- [ ] Export trade history as CSV
- [ ] Optional real trading mode with API key authentication

---

## Contributing

Contributions are welcome! Please open an issue or submit a pull request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

---

## License

This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.

---

## Disclaimer

This software is provided "as is" for educational and research purposes. It does **not** constitute financial advice. The authors are not responsible for any financial losses incurred through the use of this tool. Prediction market trading carries inherent risks. Always trade responsibly and within your means.
