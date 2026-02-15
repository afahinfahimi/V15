# Module3 - Signals

**Lovable Trade V16 — Updated 2/13/2026**

## Signals Overview
- This module considers all available results and information and issues Action Signals
- Analysis and Market Scores as well as VIX are needed before the Signals are determined and issued.
- Follow the phases below to determine the final signal to be issued
- Each phase/condition has defenition of the condition, Signals about actions to take, and how to manage your current holdings.

## PHASE 1: VIX Override 

If VIX is over 25, that overrides everything else. Take actions below.

| VIX Condition | MS Condition | AS Condition | Signal Title | Signal Details | Manage Holdings | Cash % | Color | 
|---------------|--------------|--------------|--------------|----------------|-----------------|--------|-------|
| VIX ≥ 30 | Any | > 65 | Aggressive Buy | Combine 80% Stocks + 20% QQQ | Keep holdings + Add | 5% Cash | t-green | 
| VIX ≥ 25 | Any | > 65 | Combine 70% Stocks + 30% QQQ | Keep holdings + Add | 20% Cash | t-green |

---

## PHASE 2: Market Score Override

The second group of overrides is based on Market Scores below. They trigger after VIX is checked and it is under 25.

| VIX Condition | MS Condition | AS Condition | Signal Title | Signal Details | Manage Holdings | Cash % | Color | 
|---------------|--------------|--------------|--------------|----------------|-----------------|--------|-------|
| < 25 | < 10 | Any | Weak Market | Sell everything. Ignore AS. | Consider selling most, especially if over invested | 90% |  t-red |
| < 25 | ≥ 80 | Any | No New Buys | Market seems over heated | Set 5% stop loss orders on all. Trim if under 50% cash | 50% | t-yellow |

---

## PHASE 3: Buy conditions

If no override triggered, look for the combination of Market and Analysis Scores below

| Condition | Signal Title | Signal Details | Manage Holdings | Color | 
|-----------|--------------|----------------|--------------------|-------|
| 10 to 30 | AS ≥ 75 | Buy stocks with 75+ scores | t-green | 
| 30 to 55 | AS ≥ 65 | Buy stocks with at least a AS of 65 | t-green |
| 55 to 70 | AS ≥ 65 | Selective Buys. Avoid overextended stocks. Stop Loss on holsings. | t-teal | 
| 70 to 80 | AS ≥ 75 | Careful Buy of over 75 AS. Be very mindful of Over-Heated market. Stay away of 'expensive' stocks. Tight stops on holdings. | t-blue |

---

### Position Sizing

| Condition | Signal Title | Signal Details | Manage Holdings | Color | 
|-----------|--------------|----------------|--------------------|-------|
| VIX Override (≥ 30) | Equal weight across SA 75+ stocks, fill rest in QQQ | 95% total deployed | B1 |
| VIX Override (≥ 25) | Equal weight across SA 75+ stocks, fill rest in QQQ | 80% total deployed | B1 |
| Fear Buy (MC 10–30) | Equal weight | Up to 5 stocks | B1 |
| Normal Buy (MC 30–55) | Equal weight | Up to 10 stocks | B2 |

---

## PHASE 5: Holding Rules

| Current AS | Stop Type | Action | Holding Management | Color |
|------------|-----------|--------|-------|
| ≥ 75 | None | **Hold with conviction.** No stop needed. | H1 |
| 65–75 | 10% trailing stop | **Hold with caution.** Set or adjust 10% trailing stop from recent high. | H2 |
| 55–65 | 5% trailing stop | **Hold tight.** Set or adjust 5% trailing stop from recent high. | H3 |
| < 55 | — | **Sell immediately.** Do not wait for stop. | S1 |

### Re-Score Frequency

- **Normal conditions:** Weekly (every Monday or after market close Friday).
- **MC zone change:** Re-score within 24 hours if MC crosses a zone boundary.
- **VIX spike above 20:** Re-score all holdings immediately.

---

## PHASE 6: WHEN TO SELL

### Automatic Sells (no discretion)

| # | Trigger | Action | Color | Timing |
|---|---------|--------|-------|--------|
| S1 | SA drops below 55 on weekly re-score | Sell at market open next day | S1 | Immediate |
| S2 | Trailing stop hit (10% or 5% per tier) | Sell per broker stop order | S2 | Automatic |
| S3 | Override O2 triggers (MC < 10) | Sell everything | X1 | Immediate |
| S4 | Override O3 triggers (MC ≥ 70, 3+ days) | 5% stop on all — let stops manage exit | S2 | Set and wait |

### Discretionary Sells

| # | Situation | Guidance | Color |
|---|-----------|----------|-------|
| D1 | SA 65–75 and MC moves from Normal → Extended | Consider selling weaker positions first | S3 |
| D2 | Held position hits +30% gain | Consider taking partial profits (sell half) | S3 |
| D3 | Stock held for 3+ months with SA consistently declining | Re-evaluate thesis — likely sell | S3 |

---

## PHASE 7: AFTER SELLING

| Situation | Action | Color |
|-----------|--------|-------|
| Sold due to SA < 55 | Do NOT rebuy until SA returns to buy threshold AND MC is in buy zone | W1 |
| Sold due to stop hit | Wait 5 trading days. Re-score. If SA still qualifies, may re-enter | W1 |
| Sold due to MC panic (O2) | Wait for MC to exit Panic zone (≥ 10) before any new buys | X1 |
| Took profits (D2) | Remaining half follows normal hold rules | H2 |


---

## VALIDATION STATUS

| Rule | Status | Evidence |
|------|--------|----------|
| VIX Override (O1) | ✅ MC-level validated | 6yr QQQ data. No SA-level backtest yet. |
| Panic Cash (O2) | ⚠️ Rare | Bottom 3%, very few occurrences |
| Euphoria Stop (O3) | ⚠️ Unvalidated | Based on distribution, no 3-day persistence test |
| Fear Buy SA 75+ (B1) | ✅ Strongly validated | Aug-Sep backtests, 100% win at 3M |
| Normal Buy SA 65+ (B2) | ✅ Partially validated | 6yr MC data, Oct backtest |
| Extended Hold (B3) | ✅ Validated | Oct + Mar backtests confirm negative 1M returns |
| Sell Rules (S1–S4) | ❌ Unvalidated | Need daily SA re-score data |
| Hold Tiers | ❌ Unvalidated | Need daily SA re-score data |

---

## DATA GAPS — STILL NEEDED

1. Daily SA re-scores for held stocks over time
2. VIX ≥ 25 SA-level backtest (need backtest from April 2025 crash or similar)
3. MC ≥ 70 sustained 3+ day test
4. Stop-loss effectiveness testing (10% vs 5% vs immediate)
5. Re-entry timing after sells

---

**End of Master Action Table**

## Decision Flowchart

```
START
  │
  ├─ Check VIX
  │   ├─ VIX ≥ 25 → Aggressive Buy (AS and MS are disregarded)
  │   └─ VIX < 25 → Continue ↓
  │
  ├─ Check MC Zone
  │   ├─ MS < 10 → Sell All. Keep cash ready.
  │   ├─ MS 10–30 → Good market buy stocks with Analysis Score ≥ 75  
  │   ├─ MS 30–55 → Great market buy stocks with Analysis Score ≥ 65 [B2]
  │   ├─ MS 55–70 → Potential over extenssion. No new buys, but hold existing 
  │   └─ MS ≥ 70 (3+ days) → Put a 5% stop loss order on everything 
  │
  ├─ When to seLL stocks you hold as long as above is not an overwrite 
  │   ├─ AS ≥ 75 → Hold, no stop 
  │   ├─ AS 65–75 → 10% trailing stop 
  │   ├─ AS 55–65 → 5% trailing stop 
  │   └─ AS < 55 → Sell immediately 
  │
  └─ END
```
