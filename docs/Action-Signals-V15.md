# Lovable Trade — Master Action Signals V15

**Complete Trading Rules — Updated 2/11/2026**

Calibrated against 6 years MC data (N=2,180 days) + 5 backtest dates (~370 stocks).

---

## COLOR CODE INDEX

Flat color reference for all action signals. Each code maps to a hex color defined in app Settings → Colors.

| Code | Intent | Description |
|------|--------|-------------|
| B1 | Aggressive Buy | VIX override or Fear zone + high SA |
| B2 | Standard Buy | Normal zone + qualifying SA |
| B3 | Watchlist | Quality stock, wrong timing |
| H1 | Hold Strong | SA ≥75, no stop needed |
| H2 | Hold Caution | SA 65–75, 10% trailing stop |
| H3 | Hold Tight | SA 55–65, 5% trailing stop |
| S1 | Sell Now | Immediate forced sell |
| S2 | Sell Managed | Stop-based or scheduled exit |
| S3 | Sell Consider | Discretionary / partial profit |
| W1 | Wait / No Action | No new buys allowed |
| X1 | Cash / Emergency | All cash, panic mode |

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

| # | Condition | Action | Color | Evidence |
|---|-----------|--------|-------|----------|
| O1a | VIX ≥ 30 | Deploy 95% capital. SA 75+ stocks first, fill remainder with QQQ. | B1 | 6yr data: +7% avg 1M, 85% win (N=150 days) |
| O1b | VIX ≥ 25 | Deploy 80% capital. SA 75+ stocks first, fill remainder with QQQ. | B1 | 6yr data: +4% avg 1M, 75% win (N=350 days) |
| O2 | MC < 10 | All cash. Sell everything. Ignore SA. | X1 | True panic — forward returns near zero |
| O3 | MC ≥ 70 for 3+ consecutive days | No new buys. Set 5% stop on all positions. | S2 | Top decile. 65-80 zone: 55% win rate — fading edge |

**Priority:** O1 beats O2. If VIX spikes above 25 while MC is very low, VIX override wins — that's the strongest buy signal.

---

## PHASE 3: WHEN TO BUY

If no override triggered, check MC zone and SA score.

| # | MC Zone | SA Requirement | Action | Color | Evidence |
|---|---------|---------------|--------|-------|----------|
| B1 | Fear (10–30) | SA ≥ 75 | **BUY** | B1 | Aug-Sep backtests: +5–7% at 1M, +30–60% at 3M, 60–100% win |
| B2 | Normal (30–55) | SA ≥ 65 | **BUY** | B2 | 6yr data: +2% avg 1M, 75% win rate. Best risk-adjusted zone |
| B3 | Extended (55–70) | — | **NO NEW BUYS. Hold existing.** | W1 | Oct + Mar backtests: -3 to -4% avg 1M, 40% win rate |
| B4 | Euphoria (≥ 70) | — | **NO NEW BUYS. Hold existing.** | W1 | Declining edge, reversal risk |
| B5 | SA ≥ 75 but MC doesn't match B1/B2 | — | **WATCHLIST** — Wait for MC to enter buy zone | B3 | Quality stock, wrong timing |

---

## PHASE 4: WHAT TO BUY

When a BUY signal triggers, rank and select stocks.

### Stock Selection Priority

1. **Sort by SA Score descending.** Highest SA first.
2. **Apply minimum SA threshold** per MC zone (75 in Fear, 65 in Normal).
3. **Sector diversification:** No more than 2 positions in the same sector.
4. **Avoid:** SA < 55 stocks regardless of any other signal.

### Position Sizing

| Condition | Allocation per Stock | Max Positions | Color |
|-----------|---------------------|---------------|-------|
| VIX Override (≥ 30) | Equal weight across SA 75+ stocks, fill rest in QQQ | 95% total deployed | B1 |
| VIX Override (≥ 25) | Equal weight across SA 75+ stocks, fill rest in QQQ | 80% total deployed | B1 |
| Fear Buy (MC 10–30) | Equal weight | Up to 5 stocks | B1 |
| Normal Buy (MC 30–55) | Equal weight | Up to 10 stocks | B2 |

---

## PHASE 5: HOW TO HOLD

Re-score all held positions weekly. Current SA score determines stop level.

### Weekly Portfolio Re-Score Rules

| Current SA | Stop Type | Action | Color |
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

## QUICK REFERENCE — DECISION FLOWCHART

```
START
  │
  ├─ Check VIX
  │   ├─ VIX ≥ 30 → Deploy 95% (SA 75+ first, QQQ fill) [B1] → DONE
  │   ├─ VIX ≥ 25 → Deploy 80% (SA 75+ first, QQQ fill) [B1] → DONE
  │   └─ VIX < 25 → Continue ↓
  │
  ├─ Check MC Zone
  │   ├─ MC < 10 → ALL CASH [X1] → DONE
  │   ├─ MC 10–30 → Buy SA ≥ 75 only [B1]
  │   ├─ MC 30–55 → Buy SA ≥ 65 [B2]
  │   ├─ MC 55–70 → No new buys, hold existing [W1]
  │   └─ MC ≥ 70 (3+ days) → 5% stops on everything [S2]
  │
  ├─ For Holdings: Weekly Re-Score
  │   ├─ SA ≥ 75 → Hold, no stop [H1]
  │   ├─ SA 65–75 → 10% trailing stop [H2]
  │   ├─ SA 55–65 → 5% trailing stop [H3]
  │   └─ SA < 55 → Sell immediately [S1]
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
