# Module3 - Signals

**Lovable Trade V16 — Updated 2/13/2026**

## Signals Overview
- This module considers all available results and information and issues Action Signals.
- Analysis (SA) and Market (MC) Scores as well as VIX are needed before the Signals are determined and issued.
- Follow the phases below to determine the final signal.
- Each phase describes a combination of conditions and how they are translates to Action Signals for new trades and managing existing positions.

## PHASE 1: VIX Override 

If VIX is over 25, that overrides everything else. Take actions below.

| VIX Condition | MC Scores | SA Scores | Signal Title | Signal Details | Manage Holdings | Cash at Hand | Color | 
|---------------|--------------|--------------|--------------|----------------|-----------------|--------|-------|
| VIX ≥ 30 | Any | >= 65 | Aggressive 65+ Buy | Combine 80% Stocks + 20% QQQ | Keep holdings + Add | 5% | t-green | 
| VIX ≥ 25 | Any | >= 65 | Buy 65+ | Combine 70% Stocks + 30% QQQ | Keep holdings + Add | 20%  | t-green |
| VIX ≥ 25 | Any | < 65 | Find 65+ Alternatives | Good buying opportunity for better stocks | Sell below 65 and buy better quality or buy QQQ | 20% | t-teal |

---

## PHASE 2: Market Score Override

The second group of overrides is based on Market Scores below. They trigger after VIX is checked and it is under 25.

| VIX Condition | MC Scores | SA Scores | Signal Title | Signal Details | Manage Holdings | Cash at Hand | Color | 
|---------------|--------------|--------------|--------------|----------------|-----------------|--------|-------|
| < 25 | < 15 | >= 75 | Weak Market | Don't open new positions. | Add tight 5% trailing stop | 90% |  t-orange |
| < 25 | < 15 | 65-74 | Weak Market | Don't open new positions. | Sell all or add tight 5% trailing stop | 90% |  t-red |
| < 25 | ≥ 80 | >= 70 | No New Buys | Market seems over heated | Set 10% trailing stop loss | 50% | t-yellow |
| < 25 | ≥ 80 | < 70 | No New Buys | Market seems over heated | Set 5% trailing stop loss | 50% | t-orange |

---

## PHASE 3: Regular Market

If no override triggered, look for the combination of Market and Analysis Scores below

| VIX Condition | MC Scores | SA Scores | Signal Title | Signal Details | Manage Holdings | Cash at hand | Color | 
|---------------|--------------|--------------|--------------|----------------|-----------------|--------|-------|
| < 25 | >= 15 | ≥ 75 | Buy Opportunity | Healthy market to add quality positions | Hold quality and add new positions | 20% | t-green | 
| < 25 | 15 to 54 | 65 to 74 | Buy Opportunity | Healthy market to add quality positions | Hold quality and add new positions | 20% | t-green | 

| < 25 | 55 to 69 | ≥ 65 | Selective Buys | Avoid overextended stocks | Put stop loss | 50% | t-teal | 
| < 25 | 70 to 80 | ≥ 75 | Careful Buy | Overheated market. Stay away of 'expensive' stocks. | Tight stop loss | 50% | t-blue |

---

---

## PHASE 3: Analysis Score Override

This signal applies to individual stocks and not the whole market. 
If SA scores for a stock goes/is under 55 that is a 'Sell' signal. 
If you are in doubt, you can wait to get a confirmtaion when the stock stays under 55 for more than two days.

| VIX Condition | MC Scores | SA Scores | Signal Title | Signal Details | Manage Holdings | Cash at Hand | Color | 
|---------------|-----------|-----------|--------------|----------------|-----------------|--------------|-------|
| Any | Any | < 55 | Poor Quality Stock | Regardless of market condition, this stock has a low score | Sell immediately. | Determined by market status |  t-red |









## PHASE 4: Holding Rules Based on Analysis Score
To handle existing positions first check the market 'overrides' in phase 1 and 2. If they don't apply SA scores come into play.

| VIX | MC Score | SA Score | Action |
|------------------------|--------|
| > 25 | Any | Any | Hold All |
| < 25 | < 10 | < 65 | Sell All | 
| < 25 | < 10 | >= 65 | Hold All | 
| < 25 | 10-55 | > 65 | Hold All | 
| Any | Any | > 55 | Sell All | 
| Any | Any | > 75 | Hold All |

≥ 75 | None | 
| 65 to 75 | 10% trailing stop | 
| 55 to 65 | 5% trailing stop | 
| < 55 | Sell immediately |

---

**End of Master Action Table**

