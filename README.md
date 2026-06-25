# 🧠 SMC Liquidity Flow Engine

![Platform](https://img.shields.io/badge/Platform-GetAgent%20Studio-blue)
![Asset](https://img.shields.io/badge/Asset-BTCUSDT-orange)
![Exchange](https://img.shields.io/badge/Exchange-Bitget-purple)
![Leverage](https://img.shields.io/badge/Leverage-3×-red)
![Status](https://img.shields.io/badge/Status-Paper%20Trading-yellow)
![Hackathon](https://img.shields.io/badge/Bitget%20AI-Hackathon%20Submission-brightgreen)

> A sequential Smart Money Concepts (SMC) algorithmic trading strategy for BTCUSDT perpetual futures on Bitget, deployed via GetAgent Studio.

🔗 **Live Strategy:** [View on GetAgent Studio](https://getagent.studio/strategy/9995a7c3-215b-4a65-a6c0-ed1c36b1c074)  
📂 **GitHub:** [Skyemirates/SMC-Liquidity-flow-Engine](https://github.com/Skyemirates/SMC-Liquidity-flow-Engine/tree/main)

---

## 🏆 Bitget AI Hackathon — Project Submission

**Author:** Skyemirates  
**Asset:** BTCUSDT Perpetual Futures  
**Exchange:** Bitget  
**Platform:** GetAgent Studio (Playbook)

---

### 1. Why I Built It

Most retail algorithmic strategies are built on composite indicator scores — they fire when enough signals overlap. The problem is that these approaches generate noise. They enter on statistical coincidence rather than on a coherent market narrative.

Smart Money Concepts (SMC) has gained traction among discretionary traders precisely because it tells a *story* about price: institutions engineer liquidity, reverse price through a structural break, then retest their own footprint. That sequence is logical, repeatable, and observable.

The SMC Liquidity Flow Engine was built to codify that story into a strict, sequential decision engine — not a score, but a chain. Every stage must complete before the next is considered. The result is fewer trades, but each one grounded in a complete institutional narrative rather than indicator overlap.

The choice of BTCUSDT perpetual futures on Bitget was deliberate: BTC is the most liquid crypto asset, perpetual futures allow both long and short exposure, and Bitget's infrastructure provides the execution environment needed for a strategy that depends on precise entry timing around liquidity zones.

---

### 2. Why the Strategy Works — Core Logic & Signals

**Signal Type: Pure Price Action (Technical — SMC Framework)**

This strategy uses **no on-chain data, no macro feeds, no sentiment indicators, and no traditional technical indicators** (no RSI, MACD, Bollinger Bands, etc.). Every signal is derived from raw OHLCV price structure. SMC theory holds that price itself encodes institutional intent, and adding external indicators introduces lag and noise to signals already visible in the candles.

**The Four-Stage Decision Chain**

**Stage 1 — Liquidity Sweep**  
Institutions require large pools of resting orders to fill their positions. These pools cluster at obvious swing highs and lows where retail traders place stops. The engine detects when price breaches one of these levels and then closes back inside — confirming a deliberate liquidity grab, not a genuine breakout.
- Long trigger: price wicks below a swing low / equal-low cluster → closes above it
- Short trigger: price wicks above a swing high / equal-high cluster → closes below it

**Stage 2 — Change of Character (CHoCH)**  
A liquidity sweep alone is not enough. CHoCH is the first swing high exceeding the prior high (bullish) or swing low breaking the prior low (bearish), occurring *after* the sweep. A ranging-structure filter rejects setups where the swing range is too compressed.

**Stage 3 — Retest of Origin (Order Block / FVG)**  
After CHoCH, price retraces to the origin of the impulsive move. The engine waits for price to pull back into either an **Order Block** (last opposing candle before the impulsive move) or a **Fair Value Gap** (three-candle imbalance left by fast institutional moves).

**Stage 4 — Confirmation Filter**  
The entry candle must: (1) close in the intended direction with body ≥ 40% of total range, and (2) sit in the correct Fibonacci discount/premium zone. A **Kill Zone** module (London 02:00–05:00 UTC / New York 07:00–10:00 UTC) boosts confidence during high-volume sessions.

```
Liquidity Sweep Detected
        ↓
Change of Character Confirmed (within choch_max_bars)
        ↓
Price Retraces into Order Block or FVG
        ↓
Entry Candle Confirms Direction + Fibonacci Zone Valid
        ↓
ENTER POSITION
```

If any stage fails or expires (`setup_expiry_bars` = 40), the setup is invalidated and the engine resets.

---

### 3. Risk Management

| Rule | Detail |
|---|---|
| Position sizing | 2% of the 10,000 USDT margin budget per trade (~$200 risk) |
| Leverage | 3× — balancing capital efficiency with drawdown tolerance |
| Stop loss | Beyond the swept liquidity extreme |
| Take profit | Opposite liquidity pool or next major order block |
| Minimum RR | 2:1 |
| Trailing stop | Activates at 1:1 reward milestone |
| Setup expiry | Invalidated after 40 bars — prevents stale setups |

The 2% rule ensures 10 consecutive losses reduce the account by ~18%, not a wipeout.

---

### 4. Key Development Challenges & Solutions

**Challenge 1 — Defining "Equal Highs/Lows" Algorithmically**  
Discretionary SMC traders apply visual tolerance when identifying equal levels. Solution: the `equal_hl_tolerance` parameter (default 0.2%) — levels within this range are treated as equal. Parameterized for backtesting optimization.

**Challenge 2 — CHoCH Noise in Ranging Markets**  
Sideways markets constantly break swing highs/lows. Solution: a ranging-structure filter requires the prior swing range to exceed a minimum threshold before CHoCH is considered valid.

**Challenge 3 — Order Block vs. FVG Priority**  
OBs and FVGs can overlap or conflict simultaneously. Solution: the retest module flags *all* valid zones within `retest_lookback` bars — price entering any of them satisfies Stage 3.

**Challenge 4 — Setup Staleness**  
A sweep + CHoCH can remain pending for many bars. Solution: `setup_expiry_bars` = 40 cancels any setup that hasn't fully triggered, forcing the engine to reset to current market context.

---

### 5. Completed Features, Missing Features & Next Steps

**✅ Completed**
- Four-stage sequential entry logic (Sweep → CHoCH → OB/FVG Retest → Confirmation)
- Kill Zone session filter (London / New York)
- Fibonacci premium/discount zone filter
- Fixed fractional position sizing (2% risk per trade)
- Hard stop loss beyond liquidity extreme
- Take profit at opposite liquidity pool / order block
- Trailing stop at 1:1 RR milestone
- Full parameter configurability
- Paper trading deployment on GetAgent Studio (Playbook)

**🔲 In Progress / Missing**
- Multi-timeframe confirmation (4H bias confirming 1H entries)
- Volume anomaly filter on sweep candles
- Dynamic equal-level clustering algorithm
- Backtest extension to 2022–2026 (bear market regime coverage)
- Real-time alert/notification system

**🚀 Next Steps**
1. Implement multi-timeframe structural bias filter
2. Add volume anomaly detection to Stage 1
3. Extend backtest to 2022–2026
4. Apply logic to ETHUSDT and other high-liquidity perpetuals
5. Walk-forward parameter optimization (out-of-sample)
6. Move to live trading with reduced sizing once sufficient win rate data is collected

---

### 6. Frameworks, Models & APIs

| Component | Tool / Framework |
|---|---|
| Strategy deployment | GetAgent Studio — **Playbook** |
| Exchange integration | Bitget Perpetual Futures API |
| Bitget AI tool used | **Playbook** (GetAgent Studio) |
| Backtesting | GetAgent Studio built-in backtest engine |
| Price data | BTCUSDT OHLCV via GetAgent Studio catalog API |
| Logic | Sequential rule engine — no ML, pure price action |
| Version control | GitHub |

> No Skill Hub, MCP Server, Agent Hub, or US Stocks Data API were used in this version.

---

### 7. Experience with Bitget AI Tools & Views on Agentic Trading

GetAgent Studio's Playbook interface made it possible to go from strategy idea to deployed paper trade without writing boilerplate infrastructure. The integrated backtest + live performance view in one dashboard is genuinely useful for diagnosing when and why a strategy underperforms.

**What worked well:** Fast iteration, clean parameter UI, unified backtest and paper trading view.

**Suggestions:** Multi-timeframe backtest data, per-trade stage annotations on the chart, walk-forward optimization tooling, and richer execution logs showing which stage triggered each entry.

**On the future of agentic trading:** The most important near-term development isn't autonomous profit — it's **autonomous adaptation**. A strategy that can observe its own performance, identify which market regimes it fails in, and adjust within guardrails will be far more durable than a static rule set. SMC logic is well-suited to agentic frameworks because it's already narrative and sequential — an agent can explain *why* it entered and *why* it exited, providing interpretability that black-box ML cannot. The key challenge is **trust calibration**: knowing when to let the agent run and when to intervene. Platforms like GetAgent Studio are building in the right direction.

---

### 8. Submission Links

| Requirement | Link |
|---|---|
| GitHub Repository | https://github.com/Skyemirates/SMC-Liquidity-flow-Engine/tree/main |
| GetAgent Studio Strategy | https://getagent.studio/strategy/9995a7c3-215b-4a65-a6c0-ed1c36b1c074 |
| Paper trading log | Execution history in GetAgent Studio + GitHub repo |
| Repost of Bitget campaign | *(Add link here)* |
| Project post #BitgetHackathon | *(Add link here — must tag @BitgetAI)* |

---

## 📖 Overview

The SMC Liquidity Flow Engine mirrors how professional institutional traders read the market. Rather than scoring multiple signals simultaneously, it enforces a **strict, four-stage sequential chain** of price events before opening any position — resulting in fewer but higher-conviction trades.

| Detail | Value |
|---|---|
| Asset | BTCUSDT Perpetual Futures |
| Exchange | Bitget |
| Platform | GetAgent Studio |
| Leverage | 3× |
| Capital | 10,000 USDT |
| Session Focus | London & New York Kill Zones |

---

## ⚙️ How It Works

All four stages must complete within the configured lookback window for a trade to trigger.

### Stage 1 — Liquidity Sweep Detection
- **Long:** Price dips below a confirmed swing low or equal-low cluster, then closes back above.
- **Short:** Price spikes above a confirmed swing high or equal-high cluster, then closes back below.

### Stage 2 — Change of Character (CHoCH)
- **Bullish CHoCH:** New swing high forms above the prior high.
- **Bearish CHoCH:** New swing low forms below the prior low.
- A ranging-structure filter rejects setups where the swing range is too compressed.

### Stage 3 — Retest Module
Once CHoCH fires, the engine scans recent bars for **Order Blocks** and **Fair Value Gaps (FVGs)**, then waits for price to pull back into one of these zones.

### Stage 4 — Confirmation Filter
The entry bar must close in the intended direction with a meaningful body-to-range ratio, sit in the correct Fibonacci zone, and optionally align with a **Kill Zone** session.

---

## 🛡️ Risk Management

| Rule | Value |
|---|---|
| Risk per trade | 2% of margin budget |
| Leverage | 3× |
| Minimum risk-reward | 2:1 |
| Stop loss | Beyond the swept liquidity extreme |
| Take profit | Opposite liquidity pool or next major order block |
| Trailing stop | Activates at 1:1 reward milestone |

---

## 🔧 Parameters

| Parameter | Default | Description |
|---|---|---|
| `swing_lookback` | 8 | Bars to look back for swing highs/lows |
| `equal_hl_tolerance` | 0.002 | Tolerance for identifying equal highs/lows |
| `choch_max_bars` | 30 | Max bars allowed between sweep and CHoCH |
| `retest_lookback` | 20 | Bars to scan for order blocks and FVGs |
| `confirmation_body_ratio` | 0.4 | Min body-to-range ratio for entry candle |
| `discount_threshold` | 0.5 | Fibonacci level defining discount zone |
| `premium_threshold` | 0.5 | Fibonacci level defining premium zone |
| `risk_reward_ratio` | 2.0 | Minimum take-profit to stop-loss ratio |
| `risk_per_trade` | 0.02 | Fraction of margin budget risked per trade |
| `leverage` | 3 | Account leverage multiplier |
| `margin_budget` | 10,000 | Total margin in USDT |
| `starting_capital` | 10,000 | Starting capital in USDT |
| `setup_expiry_bars` | 40 | Bars before a pending setup expires |

---

## 📊 Live Performance

> Paper trading since **Jun 22, 2026** · Period: Feb 2026 – Jun 2026

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

| # | Direction | Entry | Entry Price | Exit | Exit Price | Qty | P&L | Return | Hold |
|---|---|---|---|---|---|---|---|---|---|
| 1 | Long | Mar 2, 09:00 AM | $66,252.20 | Mar 2, 02:00 PM | $65,408.30 | 0.253 | -$233.49 | -1.27% | 5h |
| 2 | Long | Feb 27, 04:00 AM | $67,568.00 | Feb 28, 07:00 AM | $63,177.80 | 0.058 | -$259.18 | -6.50% | 1d 3h |

> ⚠️ Only 2 completed trades. Sample size is statistically insufficient to evaluate strategy performance.

---

## ⚠️ Known Risks

- **Low trade frequency** — The strict four-stage requirement can produce extended quiet periods.
- **False sweeps** — Thin sessions can produce sweep-like moves that don't reflect genuine institutional activity.
- **SMC approximation** — Algorithmic detection may not perfectly replicate discretionary SMC analysis.
- **Leverage risk** — 3× leverage amplifies both gains and losses.
- **Backtest decay** — Historical results do not guarantee future live performance.

---

## 📁 Project Structure

```
SMC-Liquidity-flow-Engine/
├── README.md                  # Project documentation (this file)
├── HACKATHON_SUBMISSION.md    # Full hackathon write-up
└── ...                        # Strategy source files
```

---

## 🚀 Deployment

1. Clone or fork this repository.
2. Import the strategy into [GetAgent Studio](https://getagent.studio).
3. Connect your Bitget account.
4. Configure parameters as needed.
5. Run in paper mode before going live.

---

## ❗ Disclaimer

This project is for **educational and research purposes only**. It is not financial advice. Paper trading results do not guarantee live performance. Leveraged trading carries significant risk of loss. Only trade with capital you can afford to lose entirely.

---

## 👤 Author

**Skyemirates**  
Strategy published: Jun 22, 2026  
Platform: [GetAgent Studio](https://getagent.studio/strategy/9995a7c3-215b-4a65-a6c0-ed1c36b1c074)
