# 📈 Quant Trading Strategy Engine

> *"A missed trade costs nothing. A bad trade costs real money."*

Most trading tools scream **BUY** or **SELL** on every tick.
This one knows when to shut up.

Built by a quant who got tired of strategies that work great — until they don't.
The engine reads the market's mood first, picks the right weapon, then decides whether the signal is actually worth acting on.
**HOLD is not a bug. It's the feature.**

---

##  What does it actually do?

```
Your stock data  →  Trend detection  →  Strategy selection  →  BUY / SELL / HOLD
```

In plain English:

1. **Reads the market regime** — Is the stock going up, going down, or doing absolutely nothing useful? Five independent classifiers vote on it.
2. **Picks the right strategy** — No single strategy works in all conditions. The engine ranks all 9 by how well they fit the current regime.
3. **Generates a signal** — Only fires BUY or SELL when three things agree: technical consensus, fundamental score, and regime confidence. Otherwise? HOLD.

---

## Quickstart

```python
import pandas as pd
from quant_trading_system import QuantTradingEngine

# Your OHLCV data — Open, High, Low, Close, Volume
df = pd.read_csv("AAPL.csv", index_col="Date", parse_dates=True)

fundamentals = {
    "pe_ratio":       22.0,
    "revenue_growth": 12.5,   # as a percentage
    "debt_equity":    0.40,
    "dividend_yield": 1.5,
}

engine = QuantTradingEngine(
    price_df     = df,
    fundamentals = fundamentals,
    ticker       = "AAPL",
    initial_cash = 100_000,
)

result = engine.run()
```

That's it. The engine prints a full report and returns a clean dictionary with everything — signal, confidence, trade parameters, backtest results, and the reasoning behind the decision.

---

## Architecture

The engine is split into 9 components, each doing exactly one job:

| Component | Job |
|---|---|
| `DataEngine` | Loads, validates, and cleans OHLCV data |
| `FeatureEngine` | Computes 30+ technical indicators |
| `TrendClassifier` | Detects market regime via 5-method ensemble vote |
| `StrategyLibrary` | 9 strategies, each tuned for specific regimes |
| `StrategySelector` | Ranks strategies by regime fit × backtest Sharpe |
| `Backtester` | Vectorised backtest with stops, slippage, and commissions |
| `RiskManager` | ATR-based stop-loss, take-profit, and position sizing |
| `SignalEngine` | Consensus signal with fundamental overlay |
| `Reporter` | Full terminal report with trade log |

---

## The 9 Strategies

Each strategy is built for specific market conditions. Using the wrong one in the wrong regime is how people lose money.

| # | Strategy | Works best in |
|---|---|---|
| 01 | Golden Cross EMA 20/50 | ▲ Uptrend |
| 02 | MACD Trend Filter | ▲▼ Both |
| 03 | Triple MA Alignment | ▲ Uptrend |
| 04 | RSI Momentum Breakout | ▲▼ Both |
| 05 | Stochastic Swing | ◆ Sideways |
| 06 | Bollinger Band Reversion | ◆ Sideways |
| 07 | RSI Oversold / Overbought | ◆ Sideways + ▼ Down |
| 08 | Donchian Breakout | ▲ Uptrend |
| 09 | Bear Reversal | ▼ Downtrend |

 **Deep-dive explanations for every strategy →** [`STRATEGY_GUIDE.pdf`](./STRATEGY_GUIDE.pdf)

---

## What makes it gain-optimised?

Most backtests look good because they trade too much. This engine is designed to only take trades that deserve to be taken.

**Five gain filters stack on every signal:**

- **Regime confidence gate** — if the trend classifier isn't at least 40% confident, no signal fires
- **Position-state consensus** — signals are based on whether you're *currently in a trade*, not just today's crossover bar
- **Sharpe-weighted voting** — strategies with better recent performance get more say in the consensus
- **Fundamental overlay** — BUY signals require fundamentals to agree (P/E, revenue growth, D/E ratio)
- **Drawdown circuit-breaker** — trading halts automatically if portfolio drawdown exceeds 15%

---

##  Sample output

```
════════════════════════════════════════════════════════════════════
  QUANTITATIVE TRADING STRATEGY ENGINE
  Ticker: AAPL  |  2023-01-02 → 2025-01-15
  Rows: 501  |  Last Close: $198.47
════════════════════════════════════════════════════════════════════

TREND REGIME ─────────────────────────────────────────────────────
  Detected: ▲ UPTREND (BULLISH)  Confidence: 80%  ████████████░░░

  MA Slope     : UPTREND     ADX/DI       : UPTREND
  BB Width     : SIDEWAYS    Price vs MA  : UPTREND
  Linear Reg   : UPTREND     Votes UP: 4  Votes SIDEWAYS: 1

TOP STRATEGIES ────────────────────────────────────────────────────
  [1] MACD Trend Filter       Sharpe: 2.23  Return: +4.1%  WinRate: 67%
  [2] RSI Momentum Breakout   Sharpe: 1.89  Return: +3.2%  WinRate: 60%
  [3] Triple MA Alignment     Sharpe: 1.54  Return: +2.8%  WinRate: 55%

════════════════════════════════════════════════════════════════════
  FINAL SIGNAL: ▲ BUY
  Confidence:   73.4%  ██████████████░░░░░░
════════════════════════════════════════════════════════════════════

  Trade Parameters:
    Entry Price   : $198.47
    Stop-Loss     : $193.91  (2.3% risk)
    Take-Profit   : $207.55  (4.6% target)
    Risk/Reward   : 1 : 2.00
    Position Size : 20.0% of portfolio
```

---

## 🔧 Installation

```bash
git clone https://github.com/yourusername/quant-trading-engine
cd quant-trading-engine
pip install pandas numpy scikit-learn yfinance pandas_ta reportlab
```

---

## Files

```
quant-trading-engine/
│
├── quant_trading_system.py   ← the engine (all 9 components)
├── Untitled15.ipynb          ← Jupyter notebook with live examples
├── STRATEGY_GUIDE.pdf        ← plain-English breakdown of every strategy
└── README.md                 ← you are here
```

---

## Disclaimer

This project is for **educational and research purposes only**.
It does not constitute financial advice.
Past backtest performance does not guarantee future results.
All trading involves risk of loss.

---

## Author

**Sri Gokul Prazath** — built this because every other quant tool either over-trades, under-explains, or does both.

If the engine saves you from one bad trade, it paid for itself.

---

*Star ⭐ the repo if you found it useful. Open an issue if something breaks.*
