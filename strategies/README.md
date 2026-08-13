# Crypto Strategy Research — BTC/ETH

## Current recommended strategy: `supertrend-overlay-btc-eth.pine`

Long-only trend overlay: fully invested while a Supertrend (ATR band,
length 21, multiplier 4.0) is bullish, flat in cash (never short) while
it's bearish. No fixed stop-loss or take-profit — the goal is to capture
buy-and-hold-like upside during real uptrends while sidestepping major
drawdowns, rather than trying to out-trade the market with tight stops
that cap winners (which is what every earlier version of this research
did, and which consistently lost to simple buy-and-hold — see history
below).

**Out-of-sample results** (train/test split, held-out test period, vs raw
buy-and-hold on the same period):

| | Overlay Return | Overlay Max DD | Buy-Hold Return | Buy-Hold Max DD |
|---|---|---|---|---|
| BTC 1D | +48.8% | 41.5% | +48.0% | 53.0% |
| ETH 1D | +8.3% | 47.0% | -23.7% | 67.5% |
| BTC 4h | +7.4% | 28.1% | -23.3% | 53.4% |
| ETH 4h | +52.8% | 46.6% | +4.2% | 68.0% |

On BTC it matches buy-and-hold's return with meaningfully less drawdown;
on ETH (both timeframes) and BTC-4h it turns a buy-and-hold *loss* into a
real gain, because those windows included stretches where staying out
during a downtrend mattered.

**Honest caveats:**
- Drawdowns are still large in *absolute* terms (28-47%), because this is
  full (or `Position Size %`) exposure while in a position, not a small
  tactical bet like the earlier low-frequency systems below. It beats
  buy-and-hold's risk/return on the same asset, but it is not a
  "low-drawdown" system in isolation.
- **Only validated on BTC and ETH.** Tested and *underperformed*
  buy-and-hold on XRP and BNB in the same period — do not assume this
  generalizes to other assets without the same train/test validation.
- Past out-of-sample performance is not a guarantee of future results —
  re-validate periodically as new data comes in, and paper-trade before
  risking real capital.

### How to backtest in TradingView

1. Open TradingView → Pine Editor → paste in `supertrend-overlay-btc-eth.pine`.
2. Add to chart on BTCUSDT or ETHUSDT, 1D or 4h.
3. Open the **Strategy Tester** tab for equity curve / trade list / stats.
4. Compare against a simple buy-and-hold baseline over the same window to
   make sure the overlay is actually adding value, not just riding the
   asset's own drift.

---

## Research history (superseded, kept for context)

This strategy is the result of an extended iteration process — kept here
so the reasoning behind it (and what *didn't* work) isn't lost.

**v1 — EMA trend + MACD cross** (`ema-macd-atr-crypto.pine`): first
attempt, both-direction trading with tight ATR stops/targets. Backtested
-10% on BTCUSDT 4h with high trade count and mostly small losses — the
signature of trading chop, not trend.

**v2** — added an ADX trend-strength filter, minimum EMA separation, wider
stops, and a cooldown between entries. Reduced losses but was still net
negative on a proper multi-year, multi-symbol, train/test-validated
backtest (BTC/ETH/XRP/BNB/SOL, 2017-2026): every top in-sample candidate
flipped negative out-of-sample.

**v3 — chandelier exit redesign**: replaced the fixed take-profit with an
ATR trailing stop to stop capping winners early. Same result: still
failed out-of-sample with the same entry logic, which pointed the
problem at the *entry signal* (EMA/MACD timing), not the exits.

**Genuinely different entry signals tested one at a time**, each with
proper train/test splits:
- **Donchian channel breakout** — worked well on BTC/ETH/XRP (this was the
  first signal to show real, consistent out-of-sample edge).
- **Trend-filtered support-bounce (mean reversion)** — buying a confirmed
  N-day-low rejection candle, but *only* when price is still above
  EMA200. Without that trend filter it was a net loser (classic
  falling-knife risk); with it, modest but real positive edge on
  BTC/ETH/BNB.
- **Volatility squeeze breakout** (Bollinger Bands inside Keltner
  Channels, trade the release) — built and tested, not carried forward.
- **Trendline break** (pivot-based diagonal support/resistance) — also
  showed real out-of-sample edge on BTC/ETH.
- **RSI mean-reversion** — tested alone on 4h, did not show a real edge
  (negative even in-sample).
- **Supertrend, both-direction (stop-and-reverse)** — the strongest
  single-indicator result on 4h data of anything tested, but still capped
  winners by shorting into what were often just pullbacks within larger
  uptrends.

A combined 3-signal portfolio (breakout + reversion + trendline break,
BTC/ETH only, capital split across sleeves) validated out-of-sample at
+4.98% net / 1.30% max drawdown over ~32 months on 1D data — a real,
low-drawdown, low-frequency result, but modest in absolute return
(~1.9% annualized) — well below buy-and-hold BTC in the same window
(+48.6%).

**The actual unlock**: every prior version traded *both* directions with
tight stops/targets, which structurally caps gains during a real trend —
useful for smoothing an equity curve, bad for capturing the large moves
that dominate crypto's actual returns. Switching Supertrend to a
**long-only, in-or-out overlay** (ride the trend fully invested, step
aside to cash — never short) captured most of buy-and-hold's upside while
avoiding its worst drawdowns, which is what finally beat the buy-and-hold
benchmark instead of just losing less badly than it.

## Disclaimer

This is a research artifact, not a finished production trading system. A
profitable backtest does not guarantee future performance — check for
overfitting, walk-forward test across multiple regimes, and paper-trade
before any real capital is involved. Not financial advice.
