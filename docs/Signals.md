# Lovable Trade — Master Action Table V15

**Complete Trading Rules — Updated 2/11/2026**

Calibrated against 6 years MC data (N=2,180 days) + 5 backtest dates (~370 stocks).

---

## PHASE 1: MARKET CHECK

Run MC Score first. Every day before any action.

### MC Zone Reference

| Zone | MC Range | Meaning |
|------|----------|---------|
| Panic | < 10 | No edge exists |
| Fear | 10–30 | Oversold opportunity |
| Normal | 30–55 | Sweet spot for buying |
| Extended | 55–70 | Market stretched |
| Euphoria | ≥ 70 | Risk of reversal |

---

## PHASE 2: OVERRIDES

Evaluate first. If any override triggers, skip all other rules.

| # | Condition | Action | Evidence |
|---|-----------|--------|----------|
| O1a | VIX ≥ 30 | Deploy 95% capital. SA 75+ stocks first, fill remainder with QQQ. | 6yr data: +7% avg 1M, 85% win (N=150 days) |
| O1b | VIX ≥ 25 | Deploy 80% capital. SA 75+ stocks first, fill remainder with QQQ. | 6yr data: +4% avg 1M, 75% win (N=350 days) |
| O2 | MC < 10 | All cash. Sell everything. Ignore SA. | True panic — forward returns near zero |
| O3 | MC ≥ 70 for 3+ consecutive days | No new buys. Set 5% stop on all positions. | Top decile. 65-80 zone: 55% win rate — fading edge |

**Priority:** O1 beats O2. If VIX spikes above 25 while MC is very low, VIX override wins — that's the strongest buy signal.

---

## PHASE 3: WHEN TO BUY

If no override triggered, check MC zone and SA score.

| # | MC Zone | SA Requirement | Action | Evidence |
|---|---------|---------------|--------|----------|
| B1 | Fear (10–30) | SA ≥ 75 | **BUY** | Aug-Sep backtests: +5–7% at 1M, +30–60% at 3M, 60–100% win |
| B2 | Normal (30–55) | SA ≥ 65 | **BUY** | 6yr data: +2% avg 1M, 75% win rate. Best risk-adjusted zone |
| B3 | Extended (55–70) | — | **NO NEW BUYS. Hold existing.** | Oct + Mar backtests: -3 to -4% avg 1M, 40% win rate |
| B4 | Euphoria (≥ 70) | — | **NO NEW BUYS. Hold existing.** | Declining edge, reversal risk |
| B5 | SA ≥ 75 but MC doesn't match B1/B2 | — | **WATCHLIST** — Wait for MC to enter buy zone | Quality stock, wrong timing |

---

## PHASE 4: WHAT TO BUY

When a BUY signal triggers, rank and select stocks.

### Stock Selection Priority

1. **Sort by SA Score descending.** Highest SA first.
2. **Apply minimum SA threshold** per MC zone (75 in Fear, 65 in Normal).
3. **Sector diversification:** No more than 2 positions in the same sector.
4. **Avoid:** SA < 55 stocks regardless of any other signal.

### Position Sizing

| Condition | Allocation per Stock | Max Positions |
|-----------|---------------------|---------------|
| VIX Override (≥ 30) | Equal weight across SA 75+ stocks, fill rest in QQQ | 95% total deployed |
| VIX Override (≥ 25) | Equal weight across SA 75+ stocks, fill rest in QQQ | 80% total deployed |
| Fear Buy (MC 10–30) | Equal weight | Up to 5 stocks |
| Normal Buy (MC 30–55) | Equal weight | Up to 10 stocks |

---

## PHASE 5: HOW TO HOLD

Re-score all held positions weekly. Current SA score determines stop level.

### Weekly Portfolio Re-Score Rules

| Current SA | Stop Type | Action |
|------------|-----------|--------|
| ≥ 75 | None | **Hold with conviction.** No stop needed. |
| 65–75 | 10% trailing stop | **Hold with caution.** Set or adjust 10% trailing stop from recent high. |
| 55–65 | 5% trailing stop | **Hold tight.** Set or adjust 5% trailing stop from recent high. |
| < 55 | — | **Sell immediately.** Do not wait for stop. |

### Re-Score Frequency

- **Normal conditions:** Weekly (every Monday or after market close Friday).
- **MC zone change:** Re-score within 24 hours if MC crosses a zone boundary.
- **VIX spike above 20:** Re-score all holdings immediately.

---

## PHASE 6: WHEN TO SELL

### Automatic Sells (no discretion)

| # | Trigger | Action | Timing |
|---|---------|--------|--------|
| S1 | SA drops below 55 on weekly re-score | Sell at market open next day | Immediate |
| S2 | Trailing stop hit (10% or 5% per tier) | Sell per broker stop order | Automatic |
| S3 | Override O2 triggers (MC < 10) | Sell everything | Immediate |
| S4 | Override O3 triggers (MC ≥ 70, 3+ days) | 5% stop on all — let stops manage exit | Set and wait |

### Discretionary Sells

| # | Situation | Guidance |
|---|-----------|----------|
| D1 | SA 65–75 and MC moves from Normal → Extended | Consider selling weaker positions first |
| D2 | Held position hits +30% gain | Consider taking partial profits (sell half) |
| D3 | Stock held for 3+ months with SA consistently declining | Re-evaluate thesis — likely sell |

---

## PHASE 7: AFTER SELLING

| Situation | Action |
|-----------|--------|
| Sold due to SA < 55 | Do NOT rebuy until SA returns to buy threshold AND MC is in buy zone |
| Sold due to stop hit | Wait 5 trading days. Re-score. If SA still qualifies, may re-enter |
| Sold due to MC panic (O2) | Wait for MC to exit Panic zone (≥ 10) before any new buys |
| Took profits (D2) | Remaining half follows normal hold rules |

---

## QUICK REFERENCE — DECISION FLOWCHART

```
START
  │
  ├─ Check VIX
  │   ├─ VIX ≥ 30 → Deploy 95% (SA 75+ first, QQQ fill) → DONE
  │   ├─ VIX ≥ 25 → Deploy 80% (SA 75+ first, QQQ fill) → DONE
  │   └─ VIX < 25 → Continue ↓
  │
  ├─ Check MC Zone
  │   ├─ MC < 10 → ALL CASH → DONE
  │   ├─ MC 10–30 → Buy SA ≥ 75 only
  │   ├─ MC 30–55 → Buy SA ≥ 65
  │   ├─ MC 55–70 → No new buys, hold existing
  │   └─ MC ≥ 70 (3+ days) → 5% stops on everything
  │
  ├─ For Holdings: Weekly Re-Score
  │   ├─ SA ≥ 75 → Hold, no stop
  │   ├─ SA 65–75 → 10% trailing stop
  │   ├─ SA 55–65 → 5% trailing stop
  │   └─ SA < 55 → Sell immediately
  │
  └─ END
```

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
