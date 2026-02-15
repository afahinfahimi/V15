# Module3 - Signals
**Lovable Trade V16 — Updated 2/13/2026**

## Signals 
- This module considers all available results and information and issues Action Signals.
- Analysis (SA) and Market (MC) Scores as well as VIX are needed before the Signals are determined and issued.

## Market Conditions
There are two conditions a market is in. Normal Market and Override Mode.
The Override Mode is when VIX or MC are at a specific level that cancels out the effect of other scores.
Then Normal Market is when market is active and Signals are determined based on calculations and relationship of different scores to each other.
Below you see the three conditions possible with two groups of signals for each. One for the whole market and one for individual stocks considering their scores within that market condition.

## Universal Rule
| SA | Stock Signal | Manage Holdings | Color |
|----|--------------|-----------------|-------|
| < 55 | Find Better | Sell | t-red |

This rule applies in ALL conditions. SA < 55 always triggers Sell regardless of VIX or MC.

---

### CONDITION 1: VIX Override 

MARKET SIGNALS

| VIX Condition | MC Score | Market Signal | Market Signal Details | Cash at Hand | Color |
|---------------|----------|---------------|-----------------------|--------------|-------|
| VIX >= 30 | Any | Aggressive Buy | Fear is maximized, great buying opportunity | 5% | a-green |
| VIX 25 to 29 | Any | Confident Buy | Market panic drop, buy including QQQ positions | 20% | t-green |

STOCK SIGNALS

| VIX | MC | SA | Stock Signal | Manage Holdings | Color |
|-----|----|----|--------------|-----------------|-------|
| VIX >= 25 | Any | >= 75 | Aggressive Buy | Hold | t-green |
| VIX >= 25 | Any | 65 to 74 | Buy | Hold | t-green |
| VIX >= 25 | Any | 55 to 64 | Monitor for improvement | Consider Selling. Tight Stop loss | t-yellow |

---

### CONDITION 2: Market Score Override

MARKET SIGNALS

| VIX | MC | Signal Title | Signal Details | Cash at Hand | Color |
|-----|----|--------------|----------------|--------------|-------|
| < 25 | < 15 | Weak Market | No new positions | 90% | t-orange |
| < 25 | > 80 | Overheated | No new positions | 50% | t-yellow |

STOCK SIGNALS

| VIX | MC | SA | Stock Signal | Manage Holdings | Color |
|-----|----|----|--------------|-----------------|-------|
| < 25 | < 15 | >= 65 | No New Buys | Add tight 5% trailing stop | t-orange |
| < 25 | < 15 | 55 to 64 | Sell | Sell | t-red |
| < 25 | > 80 | >= 70 | No New Buys | Set 10% trailing stop loss | t-yellow |
| < 25 | > 80 | 55 to 69 | No New Buys | Set 5% trailing stop loss | t-orange |

---

### CONDITION 3: Regular Market

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
| < 25 | 15 to 69 | >= 75 | Buy | Hold | t-green |
| < 25 | 15 to 69 | 65 to 74 | Buy | Hold with 15% trailing stop | t-green |
| < 25 | 15 to 69 | 55 to 64 | Monitor | Hold with 10% trailing stop | t-yellow |
| < 25 | 70 to 80 | >= 75 | Buy | Hold with 10% trailing stop | t-green |
| < 25 | 70 to 80 | 65 to 74 | Careful Buy | Hold with 10% trailing stop | t-yellow |
| < 25 | 70 to 80 | 55 to 64 | Monitor | Hold with 5% trailing stop | t-orange |

---

## Tiers
Tiers defined the max position size per stock
**Tier Precedence:** If a stock qualifies for multiple tiers, assign the highest tier.
**Note:** Tiers are risk caps, not quality rankings. Score determines quality; tier determines maximum position size based on risk profile.
### Portfolio Concentration Limits

| Tier | SA | Condition | Max Position | Color |
|------|----|-----------|--------------|-------|
| T1 | ≥ 55 | Profitable + Market Cap > $50B | $100,000 | t-green |
| T2 | ≥ 50 | Profitable + Market Cap > $2B | $50,000 | t-blue |
| T3 | ≥ 50 | Market Cap > $100M + (growth OR small profitable) | $20,000 | t-yellow |
| SELL | Does not qualify for any tier | $0 — Do not buy | t-red |

---

**End of Signals Instructions**


















