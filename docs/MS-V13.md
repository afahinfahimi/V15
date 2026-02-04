
# Master Score Test - Lovable Trade V13
**Updated: February 2, 2026**

---

There are multiple ways that MS can be calculated and triggers can be applied. 
Through optimizatin and direct comparison we find the best one for best situation.
Display different MS formulas in the result sections for comparison and trading use.
Below are the ways the MS can be used.

## 1. MS Standard

```
MS = (MC + 3 × SA) / 4
```

- **Purpose:** SA-weighted quality score. Higher = better overall stock + market combination.
- **Rounded** to whole number.

### MS Standard Signals

| MS Range | Signal |
|----------|--------|
| ≥ 70 | BUY |
| 60-69 | LEAN BUY |
| 50-59 | HOLD |
| 40-49 | LEAN SELL |
| < 40 | SELL |

---

## 2. MS Contrarian

```
MS = SA + 0.75 × (100 - MC)
```

- **Purpose:** Rank BUY-eligible stocks by priority. Higher MS = buy first.
- **NOT used for:** Entry/exit decisions (Zone Table governs those)

**Logic:** The formula rewards buying quality stocks during market fear. Low MC adds up to +75 bonus points. High MC adds near-zero bonus.

| SA | MC | MS | Interpretation |
|----|----|----|----------------|
| 80 | 30 | 133 | Great stock + fear = top priority |
| 80 | 50 | 118 | Great stock + neutral = high priority |
| 80 | 75 | 100 | Great stock + hot market = no new buys (Zone Table) |
| 65 | 30 | 118 | Good stock + fear = high priority |
| 65 | 50 | 103 | Good stock + neutral = moderate priority |
| 50 | 30 | 103 | Average stock + fear = moderate priority |

---

## 3. ENTRY RULES (Zone Table)

Evaluate in priority order. Stop at first match.

| Priority | Condition | Signal | Action |
|----------|-----------|--------|--------|
| 1 | VIX ≥ 28 | 🚨 VIX OVERRIDE | Buy QQQ immediately*. Ignore SA scores. Fixed position: 50% of T1 max. Exempt from §4 VIX Adjustment table. |
| 2 | MC < 20 | 🛑 PANIC | Stand down. All cash. SA unreliable. |
| 3 | MC 20-29 + SA ≥ 55 | 🟢 BUY | Fear. Aggressive buy opportunity. |
| 4 | MC 30-39 + SA ≥ 55 | 🟢 BUY | Oversold. Buy qualifying stocks. |
| 5 | MC 40-49 + SA ≥ 55 | 🟢 BUY | Cautious. Buy qualifying stocks. |
| 6 | MC 50-59 + SA ≥ 65 | 🟡 SELECTIVE BUY | Neutral. Only strong stocks. |
| 7 | MC 60-74 | ⛔ NO NEW BUYS | Overheated. Tighten stops. |
| 8 | MC ≥ 75 | ⛔ NO NEW BUYS | Euphoria. Take profits. |

**When multiple stocks qualify:** Use MS Formula to rank. Buy highest MS first.

---

## 4. EXIT RULES

Evaluate in priority order. Stop at first match.

| Priority | Condition | Action |
|----------|-----------|--------|
| 1 | VIX < 18 + QQQ profit > 10% | Exit VIX Override QQQ position only |
| 2 | Down -20% from entry | Sell — emergency stop loss |
| 3 | SA < 45 + down >15% | Sell — deteriorating quality |
| 4 | +20% T1 / +25% T2 / +30% T3 | Sell 50%, let rest run |
| 5 | 60 days held + SA < 55 | Sell — re-evaluation failed |
| 6 | Under 20 days held | Don't exit even if down |

---

## 5. POSITION SIZING

### Tier Limits (Max Position per Stock)

| Tier | Criteria | Max Position |
|------|----------|-------------|
| T1 | SA ≥ 55 + Profitable + Market Cap > $50B | $100,000 |
| T2 | SA ≥ 50 + Profitable + Market Cap > $2B | $50,000 |
| T3 | SA ≥ 50 + Market Cap > $100M + (growth OR small profitable) | $20,000 |
| SELL | Does not qualify for any tier | $0 — Do not buy |

**T3 Qualification Path A (Unprofitable Growth):**
- Unprofitable with at least 2 of 3 growth questions scoring ≥1 pt (Q1, Q2, Q3)

**T3 Qualification Path B (Small Profitable):**
- Profitable with Market Cap $100M–$2B

**Tier Precedence:** If a stock qualifies for multiple tiers, assign the highest tier.

**Note:** Tiers are risk caps, not quality rankings. Score determines quality; tier determines maximum position size based on risk profile.


---

## MS DECISION FLOW

```
START
  │
  ├─ Check VIX ≥ 28? ──YES──► Buy QQQ at 50% T1 max. Done.
  │
  ├─ Check MC < 20? ──YES──► Stand down. All cash. Done.
  │
  ├─ Check SA/MC Vetoes? ──YES──► AVOID. Done.
  │
  ├─ Check Zone Table (Entry Rules)
  │    │
  │    ├─ BUY eligible? ──YES──► Calculate MS = SA + 0.75×(100-MC)
  │    │                          Rank by MS. Buy highest first.
  │    │                          Apply Tier + VIX position sizing.
  │    │
  │    └─ NO NEW BUYS? ──YES──► Hold existing. Monitor exits.
  │
  └─ Check Exit Rules for held positions
```

---


**End of Master Score V12**
