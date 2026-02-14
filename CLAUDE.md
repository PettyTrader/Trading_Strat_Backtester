# CLAUDE.md — AI Assistant Guide for Trading_Strat_Backtester

## Project Overview

A modular BTC backtesting framework for developing and validating trading strategies. The engine accepts strategy definitions as dictionary configurations and tests them across multiple timeframes (15m, 1H, 4H, 1D, 1W, 1M) with realistic fee/slippage modeling, statistical validation, and interactive HTML reporting.

**Status**: Early development — project structure and core modules are being built.

## Repository Structure

```
Trading_Strat_Backtester/
├── core/           # Backtesting engine, execution logic, trade management
├── strategies/     # Individual strategy definitions (dict configs)
├── data/           # Price data, cached OHLCV files
├── indicators/     # Technical indicator calculations
├── validation/     # Permutation testing, walk-forward, Monte Carlo modules
├── visualization/  # Charting, reporting, HTML generation
├── results/        # Saved backtest outputs (HTML reports, CSVs) — gitignored
├── utils/          # Helper functions, fee calculations
├── README.md
└── CLAUDE.md
```

## Tech Stack

- **Language**: Python 3.11+
- **Data source**: Kraken via `ccxt` (Binance migration planned)
- **Core libraries**: `ccxt`, `pandas`, `numpy`, `plotly`
- **No third-party backtest frameworks** — engine is built from scratch for full control

## Coding Conventions

### Style Rules

- Type hints on **all** function signatures
- Docstrings on all public functions explaining parameters and returns
- Descriptive variable names — no single-letter names except loop indices (`i`, `j`)
- Files should stay focused and modular, preferably under 300 lines
- Functions should do one thing well

### Architecture Principles

- Strategies are **dictionary-based configs**, not code:
  ```python
  strategy = {
      "name": "Strategy Name",
      "entry_long": "rsi < 30 and close > sma_50",
      "exit_long": "rsi > 70",
      "entry_short": None,
      "exit_short": None,
      "stop_loss": 0.02,
      "take_profit": 0.04,
      "indicators": ["rsi", "sma_50"]
  }
  ```
- The core engine **must never need modification** to add a new strategy
- Position sizing is fixed initially; architecture must support future upgrade to percent-equity or risk-based sizing
- Fee structure is configurable (Kraken maker/taker fees by default) to support exchange migration
- Slippage modeling is configurable with a conservative default
- Each component should be **independently testable**

### Output Conventions

- Interactive HTML charts via Plotly (single-file, browser-openable)
- Summary HTML reports combining stats, equity curve, and drawdown
- Trade-by-trade CSV export for external analysis
- All outputs saved to `/results` with timestamps

## Key Metrics the System Calculates

### Core
Total ROI, win rate, profit factor, Sharpe ratio, max drawdown, average winner/loser, expectancy (avg R-multiple), consecutive wins/losses.

### Advanced
Sortino ratio, Calmar ratio, MAE/MFE per trade, trade efficiency, R-multiple distribution, System Quality Number (SQN), recovery factor, Monte Carlo confidence intervals.

## Development Phases

1. **Foundation** — Project structure, data ingestion (Kraken/ccxt), OHLCV caching, basic indicators (RSI, SMA, EMA)
2. **Core Engine** — Backtest loop, strategy parser (dict to executable rules), trade execution sim, fee/slippage modeling, position tracking
3. **Basic Output** — Core metrics calculation, trade log, basic equity curve, CSV export
4. **Advanced Visualization** — Interactive trade chart with entries/exits, drawdown chart, summary dashboard HTML, R-multiple histogram
5. **Validation** — Permutation testing, walk-forward analysis, Monte Carlo simulation, statistical significance reporting
6. **Multi-Timeframe** — Automatic multi-TF testing, comparison reports, optimal timeframe identification

## Common Commands

```bash
# Install dependencies (once requirements.txt exists)
pip install -r requirements.txt

# Run a backtest (planned entry point)
python -m core.backtest --strategy strategies/<name>.py

# Run tests
pytest
```

## Important Guidelines for AI Assistants

- **Read before modifying** — always read a file before proposing changes.
- **Keep it modular** — strategy definitions stay in `/strategies`, indicators in `/indicators`, etc.
- **No over-engineering** — this is a learning project; prioritize clarity over cleverness.
- **Don't touch the core engine** to support a new strategy — if a strategy can't be expressed as a dict config, extend the parser instead.
- **Document trade-offs** — when making design decisions, leave comments or notes explaining why.
- **Avoid introducing dependencies** beyond the core stack (`ccxt`, `pandas`, `numpy`, `plotly`) without explicit approval.
- **Results directory** should be treated as ephemeral output — never commit generated reports or cached data.
- **Security** — never hardcode API keys or secrets; use environment variables or config files excluded from version control.
