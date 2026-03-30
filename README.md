# Strategy Creator

Automated grid trading strategy builder. Design, backtest, and deploy range-trading strategies across crypto, forex, stocks, and commodities.

## Quick Start

1. **Download** `StrategyCreator-Setup.exe`
2. **Run the installer** — it will check for Python, install dependencies, and create a desktop shortcut
3. **Launch** from the desktop shortcut "Strategy Creator"
4. Your browser opens at `http://localhost:5000`

**Requirements:** Python 3.10+ (download from [python.org](https://python.org) — check "Add to PATH" during install)

## Create a Strategy

1. Click **Create** in the top nav
2. Pick your asset (Bitcoin, Ethereum, Euro/Dollar, Apple, Silver, etc.)
3. Answer 4 questions: direction, risk level, time horizon, capital
4. Review the generated grid configuration
5. Click **Run Simulation** — runs 3 scenarios automatically (Bull, Crash, High Volatility)
6. Review results: P&L, drawdown, win rate, trade chart

### Advanced Options

Click **Show Advanced Options** on the Review page to fine-tune:
- Grid boundaries (price min/max)
- Position sizing (entry lots, lots at min/max)
- Scaling factors
- Trading parameters (fees, min order size, spread limits, min gain per order)
- Hedge configuration (PUT/CALL options)

## Launch Live Trading

After reviewing simulation results:

1. Click **Launch Live**
2. Select your broker
3. Enter credentials (API key or terminal login)
4. Click **Launch Trader**

### Supported Brokers

| Broker | Type | Setup |
|--------|------|-------|
| **WhiteBIT** | API key | Create key at whitebit.com/settings/api |
| **Kraken** | API key | Create key at kraken.com/u/security/api |
| **IG Markets** | API key | Create key in IG account settings |
| **FXCM** | Terminal | FXCM Trading Station must be running locally |
| **ICMarkets MT4** | Terminal | MT4 with MtApi EA on port 8222 |
| **ICMarkets MT5** | Terminal | MT5 with MtApi EA on port 8228 |
| **Darwinex** | Terminal | MT5 with MtApi EA on port 8229 |

For terminal-based brokers, the trading platform must be running on the same machine. API-based brokers (WhiteBIT, Kraken, IG) work without any terminal.

## Live Dashboard

Click **Live** in the top nav to monitor all running traders.

### Per-Trader Info
- **Status indicator**: green (online), red (bridge down), amber (position mismatch), gray (offline)
- **Position**: current lots held
- **P&L**: realized (closed trades) + unrealized (open position)
- **Entry price**: where the grid started
- **Trade count**: total executed trades

### Actions
- **Health** — check broker bridge connectivity
- **Rerun** — restart the trader (preserves state and trade history)
- **Stop** — stop the trader without deleting data
- **Delete** — stop and remove all data (state file, config, history)

### Accounting Modes
Use the dropdown to switch between:
- **LIFO (state trades)** — P&L from our trade matching (default)
- **Broker Default** — P&L from broker's balance/equity
- **Broker LIFO** — our LIFO algorithm on broker-confirmed deals

## How It Works

The strategy uses a **price grid** with position sizing that varies by price level:

- **Price drops** → buy more (accumulate at lower prices)
- **Price rises** → sell some (take profit at higher prices)
- **LIFO accounting** — each sell is matched against the most recent buy, ensuring every round-trip is profitable

Key parameters:
- `price_min` / `price_max` — grid boundaries
- `levels` — number of grid levels (more = smaller trades, more frequent)
- `entry_point_lots` — initial position size
- `lots_at_min` / `lots_at_max` — position size at grid extremes
- `scaling_factor` — controls how aggressively position grows near grid edges

## Files & Folders

```
StrategyCreator/
  StrategyCreator.bat    Launch the dashboard
  setup.bat              First-time setup (run once)
  bin/                   Trading executables
  frontend/              Web dashboard (React)
  backend/               API server (Flask)
  config/
    lean.json            Broker credentials (auto-filled by GUI)
    strategies/
      live/              Active live trading configs
      paper/             Paper trading configs
    user/                Saved strategy configs
  storage/               Trader state files (positions, trade history)
  logs/                  Trader log files
  data/                  Generated market data for backtesting
  backtests/             Backtest result history
```

## Troubleshooting

**Dashboard won't open:**
- Make sure Python is installed (`python --version` in terminal)
- Check if port 5000 is free (`netstat -an | findstr 5000`)
- Run `setup.bat` again

**Trader not connecting:**
- For API brokers: verify API key and secret are correct
- For terminal brokers: make sure the trading platform is running
- Check `logs/` folder for error details

**Backtest shows no trades:**
- Check min order size — may be too large for your capital
- Try increasing capital or reducing the number of grid levels

**Bridge down (red status):**
- The trading terminal (MT4/MT5/FXCM) may have disconnected
- Click **Rerun** to restart the trader and reconnect
- For FXCM: market must be open (Sun 22:00 - Fri 22:00 UTC)
