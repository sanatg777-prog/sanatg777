# EMA Trend + MACD Momentum (ATR Risk) — Crypto Strategy

`ema-macd-atr-crypto.pine` is a TradingView Pine Script v5 **strategy** (not
just a display indicator) for crypto on 15m or 4h charts. It's built with
`strategy()` so TradingView's built-in Strategy Tester produces equity
curve, win rate, profit factor, drawdown, etc. directly — no external
backtester required to get first results.

## Logic

1. **Trend filter** — 50 EMA vs 200 EMA (configurable) sets long/short bias.
2. **Trigger** — MACD line crossing its signal line, only taken in the
   direction of the trend filter.
3. **Risk management** — stop-loss and take-profit are sized off ATR
   (Average True Range), so the same settings scale sensibly across both
   15m and 4h without re-tuning. Default: 1.5×ATR stop, 2:1 reward:risk
   target, optional ATR-based trailing stop once in profit.
4. **Backtest window** inputs let you constrain the test period, and the
   strategy auto-flattens positions outside that window.

Defaults assume $10,000 initial capital, 10% of equity per trade, 0.1%
commission (typical crypto exchange taker fee) and 2 ticks of slippage —
all adjustable in the script inputs.

## How to backtest in TradingView (available today)

1. Open TradingView → Pine Editor → paste in `ema-macd-atr-crypto.pine`.
2. Add to chart on your crypto symbol (e.g. BTCUSDT) at 15m or 4h.
3. Open the **Strategy Tester** tab for equity curve / trade list / stats.
4. Adjust inputs (EMA lengths, ATR multipliers, R:R, date window) and
   re-run to compare.

## Backtesting via the `trader-dev` MCP server (blocked)

The `trader-dev` MCP server added to this session (`https://mcp.trader.dev/sse`)
currently reports **"Needs authentication"** and exposes no tools — there's
nothing to call yet, so it can't be used to run this backtest
programmatically. Once it's authenticated (credentials/API key/OAuth,
whichever it expects), the plan would be:

- Confirm it's genuinely a paper/sandbox environment before anything trades.
- Translate the entry/exit logic above into whatever format the server's
  backtest tool expects (likely OHLCV history + the same EMA/MACD/ATR rules).
- Run the backtest with explicit capital and risk limits, not an open-ended
  "maximize profit" objective — same guardrails discussed earlier apply.

## Disclaimer

This is a starting point for research and validation, not a finished
production trading system. A profitable backtest does not guarantee future
performance — check for overfitting, walk-forward test across multiple
regimes, and paper-trade before any real capital is involved. Not
financial advice.
