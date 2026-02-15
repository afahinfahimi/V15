# Module3 - Signals
**Lovable Trade V16 — Updated 2/13/2026**

## Signals 
- This module considers all available results and information and issues Action Signals.
- Analysis (SA) and Market (MC) Scores as well as VIX are needed before the Signals are determined and issued.

## Market Conditions
There are two conditions a market is in. Normal Market and Override Mode.
The Override Mode is when VIX or MC are at a specific level that cancelled out the effect of other scores.
Then Normal Market is when market is active and Signals are determined based on calculations and relationdhip of different scores to eachother.
Below you see the three conditions possible with two group of signals for each. One for the whole market and one for individual stocks considering their scores within that market condition.


### CONDITION 1: VIX Override 

MARKET SIGNALS
| VIX Condition | MC Score | Market Signal | Market signal Details | Cash at Hand | Color |
|---------------|----------|---------------|-----------------------|--------------|-------|
| VIX ≥ 30 | Any | Aggressive Buy | Fear is maximized, great buying opportunity | 5% | a-green |
| VIX 25 to 29 | Any | Confident Buy | Market panic drop, buy including QQQ positions | 20% | t-green |


STOCK SIGNALS
| VIX | MC | SA | Stock Signals | Manage Holdings | Color | 
|---------------|---------------|-----------------|-------|
| VIX ≥ 25 | Any | >= 75 | Aggressive Buy | Hold  | t-green | 
| VIX ≥ 25 | Any | 65-74 | Buy | Hold  | t-green | 
| VIX ≥ 25 | Any | 55 to 64 | Monitor for improvement | Consider Selling. Tight Stop loss | t-yellow |
| Any | Any | < 55 | Find Better | Sell | t-red |

---

### CONDITION 2: Market Score Override

MARKET SIGNALS
| VIX | MC | Signal Title | Signal Details | Cash at Hand | Color | 
|-----|----|--------------|----------------|--------------|-------|
| < 25 | < 15 | Weak Market | No new position | 90% |  t-orange |
| < 25 | ≥ 80 | Overheated | No new positions | 50% | t-yellow |

STOCK SIGNALS
| VIX | MC | SA | Stock Signal | Manage Holdings | Color | 
|-----|----|----|--------------|-----------------|-------|
| < 25 | < 15 | >= 65 | No New Buys | Add tight 5% trailing stop | t-orange |
| < 25 | < 15 | < 65 | Sell | Sell | t-red |
| < 25 | ≥ 80 | >= 70 | No New Buys | Set 10% trailing stop loss | t-yellow |
| < 25 | ≥ 80 | < 70 | No New Buys | Set 5% trailing stop loss | t-orange |
| Any | Any | < 55 | Find Better | Sell | t-red |

---

### CONDITION 3: Regular Market

MARKET SIGNALS
| VIX | MC | Signal Title | Signal Details | Cash at Hand | Color | 
|-----|----|--------------|----------------|--------------|-------|
| < 25 | >= 15 | Growing Market | Add quality stocks | 30% | t-teal |
| < 25 | 15 to 54 | Health Market | Good time to buy | 20% | t-green |
| < 25 | 55 to 69 | Selective Buy | Avoid Overextended Stocks | 30% | t-blue |
| < 25 | 70 to 80 | Careful Buy | Overheated Market. Ride momentum carefuly | 20% | t-yellow |

STOCK SIGNALS
| VIX | MC | SA | Stock Signal | Manage Holdings | Color | 
|-----|----|----|--------------|-----------------|-------|
| < 25 | > 20 | ≥ 75 | Buy | Hold | t-green |
| < 25 | 20 to 69 | 65 to 74 | Buy | Hold with 15% trailing stop | t-green |
| < 25 | 70 to 80 | 65 to 74 | Careful Buy | Hold with 10% trailing stop | t-yellow |
| Any | Any | < 55 | Find Better | Sell | t-red |


---

**End of Signals Instructions**



