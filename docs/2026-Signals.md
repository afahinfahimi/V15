# Module3 - Signals
**Lovable Trade V16 — Updated 2/15/2026**

## Signals 
- This module considers all available results and information and issues Action Signals.
- Analysis (SA) and Market (MC) Scores as well as VIX are needed before the Signals are determined and issued.

## Required Input Data

| Field | Source | Description |
|-------|--------|-------------|
| **SA Score** | Module 1 (Analysis) — Normalized Score | `((Raw Score + 42) / 112) * 100`. Range: 0–100 |
| **MC Score** | Module 2 (Market) — Normalized Score | `((Raw Score + 40) / 92) * 100`. Range: 0–100 |
| **VIX Level** | FMP API — `^VIX` Current Price (Level) | CBOE Volatility Index spot value |
| **Net Profit Margin** | Module 1 Q4 / FMP API — `Net Income ÷ Revenue (TTM)` | Used for Tier profitability check |
| **Market Cap** | FMP API — `/api/v3/profile/{symbol}` → `mktCap` | Full raw number (e.g., 50,000,000,000 = $50B) |
| **Annual Revenue Growth %** | Module 1 Q1 / FMP API | Used for Tier T3 "growth" condition |

---

## Market Conditions
There are two conditions a market is in. Normal Market and Override Mode.
The Override Mode is when VIX or MC are at a specific level that cancels out the effect of other scores.
Then Normal Market is when market is active and Signals are determined based on calculations and relationship of different scores to each other.
Below you see the three conditions possible with two groups of signals for each. One for the whole market and one for individual stocks considering their scores within that market condition.

## Signal Evaluation Logic

Signals are determined separately for the **Market** (one result, displayed in the top bar) and for each **Stock** (per row in the results table).

### Step 1: Determine Active Condition
- **Fields:** VIX Level (`^VIX` Price from FMP), MC Score (Module 2 Normalized)
- Evaluate in order — stop at first match:

| Priority | Condition | Trigger |
|----------|-----------|---------|
| 1 | VIX Override | VIX ≥ 25 |
| 2 | MC Override | VIX < 25 AND (MC < 15 OR MC > 80) |
| 3 | Regular Market | VIX < 25 AND MC 15–80 |

### Step 2: Issue Market Signal
- Use the **Market Signals** table from the active condition (Step 1).
- Displayed once in the top bar alongside QQQ price, MC Score, and VIX.

### Step 3: Issue Stock Signal (per stock row)
1. **Universal Rule first** — if SA < 55 → "Find Better / Sell" (t-red). Done. Skip Step 3b.
2. **If SA ≥ 55** — use the **Stock Signals** table from the active condition (Step 1).

---

## Universal Rule (Stock Signal Override)
- **Fields:** SA Score (Module 1 Normalized Score)
- **Applies to:** Every stock row, in ALL conditions, checked before condition-specific stock signals.

| SA | Stock Signal | Manage Holdings | Color |
|----|--------------|-----------------|-------|
| 35 to 55 | Find Better | Sell | t-yellow |
| 35 > | Avoid | Sell | t-red |
---

### CONDITION 1: VIX Override 
- **Trigger:** VIX Level (FMP `^VIX` Price) ≥ 25
- **Fields:** VIX Level, MC Score (Module 2 Normalized), SA Score (Module 1 Normalized)

MARKET SIGNALS

| VIX Condition | MC Score | Market Signal | Market Signal Details | Cash at Hand | Color |
|---------------|----------|---------------|-----------------------|--------------|-------|
| VIX ≥ 30 | Any | Aggressive Buy | Fear is maximized, great buying opportunity | 5% | a-green |
| VIX 25 to 29 | Any | Confident Buy | Market panic drop, buy including QQQ positions | 20% | t-green |

STOCK SIGNALS

| VIX | MC | SA | Stock Signal | Manage Holdings | Color |
|-----|----|----|--------------|-----------------|-------|
| VIX ≥ 25 | Any | ≥ 75 | Aggressive Buy | Hold | t-green |
| VIX ≥ 25 | Any | 65 to 74 | Buy | Hold | t-green |
| VIX ≥ 25 | Any | 55 to 64 | Monitor for improvement | Consider Selling. Tight Stop loss | t-yellow |

---

### CONDITION 2: Market Score Override
- **Trigger:** VIX Level (FMP `^VIX` Price) < 25 AND MC Score (Module 2 Normalized) < 15 OR > 80
- **Fields:** VIX Level, MC Score (Module 2 Normalized), SA Score (Module 1 Normalized)

MARKET SIGNALS

| VIX | MC | Signal Title | Signal Details | Cash at Hand | Color |
|-----|----|--------------|----------------|--------------|-------|
| < 25 | < 15 | Weak Market | No new positions | 90% | t-orange |
| < 25 | > 80 | Overheated | No new positions | 50% | t-yellow |

STOCK SIGNALS

| VIX | MC | SA | Stock Signal | Manage Holdings | Color |
|-----|----|----|--------------|-----------------|-------|
| < 25 | < 15 | ≥ 65 | No New Buys | Add tight 5% trailing stop | t-orange |
| < 25 | < 15 | 55 to 64 | Sell | Sell | t-red |
| < 25 | > 80 | ≥ 70 | No New Buys | Set 10% trailing stop loss | t-yellow |
| < 25 | > 80 | 55 to 69 | No New Buys | Set 5% trailing stop loss | t-orange |

---

### CONDITION 3: Regular Market
- **Trigger:** VIX Level (FMP `^VIX` Price) < 25 AND MC Score (Module 2 Normalized) 15–80
- **Fields:** VIX Level, MC Score (Module 2 Normalized), SA Score (Module 1 Normalized)

MARKET SIGNALS

| VIX | MC | Signal Title | Signal Details | Cash at Hand | Color |
|-----|----|--------------|----------------|--------------|-------|
| < 25 | 15 to 29 | Growing Market | Add quality stocks | 30% | t-teal |
| < 25 | 30 to 54 | Healthy Market | Good time to buy | 20% | t-green |
| < 25 | 55 to 69 | Selective Buy | Avoid overextended stocks | 30% | t-blue |
| < 25 | 70 to 80 | Careful Buy | Overheated market. Ride momentum carefully | 20% | t-yellow |

STOCK SIGNALS

| VIX | MC | SA | Stock Signal | Manage Holdings | Color |
|-----|----|----|--------------|-----------------|-------|
| < 25 | 15 to 69 | ≥ 75 | Buy | Hold | t-green |
| < 25 | 15 to 69 | 65 to 74 | Buy | Hold with 15% trailing stop | t-green |
| < 25 | 15 to 69 | 55 to 64 | Monitor | Hold with 10% trailing stop | t-yellow |
| < 25 | 70 to 80 | ≥ 75 | Buy | Hold with 10% trailing stop | t-green |
| < 25 | 70 to 80 | 65 to 74 | Careful Buy | Hold with 10% trailing stop | t-yellow |
| < 25 | 70 to 80 | 55 to 64 | Monitor | Hold with 5% trailing stop | t-orange |

---

## Tiers
Tiers define the max position size per stock.
**Tier Precedence:** If a stock qualifies for multiple tiers, assign the highest tier.
**Note:** Tiers are risk caps, not quality rankings. Score determines quality; tier determines maximum position size based on risk profile.

### Tier Field Definitions

| Field | Source | Definition |
|-------|--------|------------|
| SA Score | Module 1 Normalized Score | `((Raw Score + 42) / 112) * 100` |
| Profitable | FMP API — Net Profit Margin (TTM) | Net Profit Margin ≥ 0% (from Module 1 Q4 field: `Net Income ÷ Revenue`) |
| Market Cap | FMP API — `/api/v3/profile/{symbol}` → `mktCap` | Full raw number. $50B = 50,000,000,000. $2B = 2,000,000,000. $100M = 100,000,000 |
| Growth | FMP API — Annual Revenue Growth % | Annual Revenue Growth % > 0% (from Module 1 Q1 field) |
| Small Profitable | FMP API — Net Profit Margin (TTM) | Net Profit Margin > 0% AND Market Cap ≤ $2B |

### Portfolio Concentration Limits

| Tier | SA | Condition | Max Position | Color |
|------|----|-----------|--------------|-------|
| T1 | ≥ 55 | Profitable (Net Profit Margin ≥ 0%) + Market Cap > $50B | $100,000 | t-green |
| T2 | ≥ 50 | Profitable (Net Profit Margin ≥ 0%) + Market Cap > $2B | $50,000 | t-blue |
| T3 | ≥ 50 | Market Cap > $100M + (Growth: Annual Revenue Growth > 0% OR Small Profitable: Net Profit Margin > 0% AND Market Cap ≤ $2B) | $20,000 | t-yellow |
| SELL | Does not qualify for any tier | $0 — Do not buy | t-red |

---

**End of Signals Instructions**
