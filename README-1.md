# SMC Liquidity Flow Engine

> A sequential Smart Money Concepts (SMC) trading strategy for BTCUSDT perpetual futures on Bitget, built and deployed via GetAgent Studio.

---

## Overview

The SMC Liquidity Flow Engine is a rule-based algorithmic trading strategy that mirrors how professional institutional traders read the market. Rather than scoring multiple signals simultaneously, it requires a strict, sequential chain of four price events before ever opening a position — producing fewer trades but with significantly higher conviction.

**Asset:** BTCUSDT Perpetual Futures  
**Exchange:** Bitget  
**Platform:** GetAgent Studio  
**Leverage:** 3×  
**Capital:** 10,000 USDT

---

## Strategy Logic

The engine operates in four sequential stages. All four must complete within the configured lookback window for a trade to be valid.

### Stage 1 — Liquidity Sweep Detection
Identifies engineered liquidity events:
- **Long setup:** Price dips below a confirmed swing low or equal-low cluster, then closes back above it.
- **Short setup:** Price spikes above a confirmed swing high or equal-high cluster, then closes back below it.

### Stage 2 — Change of Character (CHoCH)
After a confirmed sweep, the engine looks for a structural reversal:
- **Bullish CHoCH:** A new swing high forms above the prior high.
- **Bearish CHoCH:** A new swing low forms below the prior low.
- A ranging-structure filter rejects setups where the swing range is too compressed.

### Stage 3 — Retest Module
Once CHoCH fires, the engine scans recent bars for **Order Blocks** and **Fair Value Gaps (FVGs)**, then waits for price to pull back into one of these zones.

### Stage 4 — Confirmation Filter
The entry bar must:
- Close in the intended direction with a meaningful body-to-range ratio.
- Sit in the correct Fibonacci premium or discount zone.
- Optionally, align with a **Kill Zone** (London or New York session) for higher confidence.

---

## Risk Management

| Parameter | Value |
|---|---|
| Risk per trade | 2% of margin budget |
| Leverage | 3× |
| Min risk-reward ratio | 2:1 |
| Stop loss | Beyond the swept liquidity extreme |
| Take profit | Opposite liquidity pool or next major order block |
| Trailing stop | Activates at 1:1 reward milestone |

---

## Parameters

| Parameter | Default | Description |
|---|---|---|
| `swing_lookback` | 8 | Bars to look back for swing highs/lows |
| `equal_hl_tolerance` | 0.002 | Tolerance for identifying equal highs/lows |
| `choch_max_bars` | 30 | Max bars allowed between sweep and CHoCH |
| `retest_lookback` | 20 | Bars to scan for order blocks and FVGs |
| `confirmation_body_ratio` | 0.4 | Min body-to-range ratio for entry candle |
| `discount_threshold` | 0.5 | Fibonacci level defining discount zone |
| `premium_threshold` | 0.5 | Fibonacci level defining premium zone |
| `risk_reward_ratio` | 2.0 | Minimum take profit to stop loss ratio |
| `risk_per_trade` | 0.02 | Fraction of margin budget risked per trade |
| `leverage` | 3 | Account leverage multiplier |
| `margin_budget` | 10,000 | Total margin in USDT |
| `starting_capital` | 10,000 | Starting capital in USDT |
| `setup_expiry_bars` | 40 | Bars before a pending setup expires |

---

## Live Performance Snapshot

*Period: Feb 2026 – Jun 2026 (Paper trading since Jun 22)*

| Metric | Value |
|---|---|
| Total Return | -4.9% |
| Max Drawdown | -5.1% |
| Win Rate | 0% |
| Profit Factor | 0.00× |
| Avg Round Trip | -3.89% |
| Completed Round Trips | 2 |
| Avg Hold Time | 16 hours |

### Trade History

| Direction | Entry | Exit | Qty | P&L | Return | Hold |
|---|---|---|---|---|---|---|
| Long | Mar 2 @ 66,252.20 | Mar 2 @ 65,408.30 | 0.253 | -$233.49 | -1.27% | 5h |
| Long | Feb 27 @ 67,568.00 | Feb 28 @ 63,177.80 | 0.058 | -$259.18 | -6.50% | 1d 3h |

> ⚠️ **Note:** Only 2 trades have completed. This sample size is too small to draw statistical conclusions about strategy performance.

---

## Known Risks

- **Low trade frequency** — The strict four-stage requirement means extended periods with no qualifying setups.
- **False sweeps** — Thin sessions can produce sweep-like movements that don't reflect genuine institutional activity.
- **SMC subjectivity** — Pattern approximations in code may not perfectly capture discretionary SMC logic.
- **Leverage risk** — 3× leverage amplifies both gains and losses.
- **Backtest limitations** — Historical results do not guarantee future performance.

---

## Platform

**GetAgent Studio**  
Strategy URL: [View on GetAgent Studio](https://getagent.studio/strategy/9995a7c3-215b-4a65-a6c0-ed1c36b1c074)

---

## Disclaimer

This strategy is experimental and runs in paper trading mode. It is not financial advice. Past performance, whether backtested or live, does not guarantee future results. Trade with caution and only risk capital you can afford to lose.
