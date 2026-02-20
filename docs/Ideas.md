# Optimization and Signal Ideas


## Score movements signals
# Score Movement Signal Table — V17
*Based on Nov 2025 snapshot, 62 stocks, 3-day confirmation rule*
*(Day 0 = trigger, Day 1 = still holds, Day 2 = ACT)*

---

## ENTRY SIGNALS

| Signal | Condition | MC Zone | Win% | Avg 1M% | Action |
|--------|-----------|---------|------|---------|--------|
| Best Entry | Cross above 80, holds 3 days | 30-50 | 78.4% | +11.1% | Buy |
| Best Entry | Cross above 80, holds 3 days | 70+ | 73.7% | +10.6% | Buy |
| Best Entry | Cross above 80, holds 3 days | 50-70 | 69.6% | +8.8% | Buy |
| Fear Entry | Cross above 80, holds 3 days | <30 | 66.7% | +19.3% | Buy — higher return, lower certainty |
| Secondary | Cross above 75, holds 3 days | 30-70 | 70.8% | +10-12% | Buy |
| Avoid | Cross above 75 or 70, holds 3 days | 70+ | 47-64% | +1-4% | Skip — market too extended |
| Dip Buy | Gradual drop 5pts over 4 days, lands 75+, no recovery >1pt | Any | 78-89% | +10-20% | Buy the dip |

---

## EXIT SIGNALS

| Signal | Condition | MC Zone | Win% (stock still wins) | Avg 1M% | Action |
|--------|-----------|---------|--------------------------|---------|--------|
| Strong Exit | Drop below 65, holds 3 days | 30-50 | 43.3% | -12.5% | Sell immediately |
| Strong Exit | Drop below 65, holds 3 days | 70+ | 50.0% | -8.3% | Sell |
| Strong Exit | Drop below 70, holds 3 days | 50-70 | 0.0% | -9.3% | Sell immediately |
| Strong Exit | Drop below 70, holds 3 days | 70+ | 15.4% | -10.5% | Sell immediately |
| Mild Exit | Drop below 75, holds 3 days | 50-70 | 35.0% | -1.7% | Sell |
| Ignore | Drop below 65 or 70, holds 3 days | <30 | 53-61% | -2 to +4% | Hold — market oversold, stock often recovers |
| Gradual Decline | 5pts over 4 days, lands <65, no recovery >1pt | 30-50 | 48.5% | -2.1% | Sell |

---

## NOISE — DO NOT ACT

| Condition | Why |
|-----------|-----|
| Drop below 80, recovers within 1 day | 33% of all 80-crossings — false alarm, avg +4.6% |
| Score rises 5+ points into any zone | No predictive value — worse than stocks already in zone |
| Score drops 5-10 pts, stock above 75 | Recovers, avg +14.75% — buy the dip instead |
| Any signal when MC <30 | Market weakness masking score drops — wait for MC recovery |
| Cross above 70 or lower | Below 75, signals are too weak to act on |

---

## Confirmation Rule

- **Day 0** — Score crosses threshold (trigger)
- **Day 1** — Next market open, still holds
- **Day 2** — Day after, still holds → ACT

Waiting filters out 39-46% of false signals with no meaningful cost to return.

---

## Key Boundaries

| Line | Meaning |
|------|---------|
| 80 | Elite zone entry — confirmed crossing is the primary buy signal |
| 75 | Secondary entry, only valid in MC <70 |
| 70 | No-man's land — not worth entering or exiting on its own |
| 65 | The real danger line — confirmed drop = exit regardless of origin |
| MC 30 | Below this, exit signals weaken — market, not stock, may be the cause |


---
---
---

# SA Score Optimization Ideas
**Baseline:** Oct 20, 2025 | 75+ ex Basic Materials | N=18 | Win% 55.6%

-----

**Idea 1:** Sector filter — exclude Basic Materials from buy signals
- Apply in Signals module: suppress Buy signal for Basic Materials regardless of SA score

-----

**Idea 2:** Add P/E Valuation Question
- New question between Q23 and Q24. Max +5 / Min -2. Fields: P/E Ratio (TTM)
- New raw score span: Max 75, Min -42 → normalize as `((raw + 42) / 117) * 100`

|P/E Range|Points|
|---------|------|
|15–35    |+5    |
|0–15     |+4    |
|35–50    |+4    |
|50–60    |+2    |
|60–80    |0     |
|80–100   |-2    |
|>100     |-5    |
|Negative |-2    |
|N/A      |0     |

-----

**Idea 3:** Require Q12 (EPS Growth) ≥ 2 as minimum gate for Buy signals at 75+
- Stocks with Q12 = 0 or 1 at high SA scores had weak forward returns vs peers with Q12 ≥ 2
- Apply in Signals module: if SA ≥ 75 and Q12 < 2 → downgrade signal to Monitor

-----

**Idea 4:** Selective Buy signal threshold raised to SA ≥ 80 — stocks 70-79 are not actionable

-----

**Idea 5:** When MC drops below 15, exit any stock whose score crossed below 70 since entry
- Simple threshold — no need to track prior score, just watch if it falls under 70

**Idea 6:** When MC drops below 15, exit any stock whose score dropped ≥6 pts since entry
- Catches high-scorers (80+) that stay above 70 but are deteriorating
- Combine with Idea 5 for maximum coverage — either condition triggers exit

-----

**Idea 7:** Quality Gate for 65-79 — require Q4≥3 AND Q3≥1 before issuing buy signals
- Q4≥3 = net margin ≥15%, Q3≥1 = positive cash flow growth
- Tested Oct 20: catches 11 FPs, loses 5 winners, win rate 37%→41% — needs more dates

**Idea 8:** Cap Q21 (Bollinger %B) at 4 points maximum
- Removes score inflation from extreme Bollinger readings without affecting breakouts
- Tested Oct 20: zero collateral damage at cap=4 — needs more dates

-----

**Idea 9:** Remove Q28 (Low Float Risk) — 94% of stocks score 0, zero variance, zero predictive value

**Idea 10:** Investigate Q31 inversion — penalized stocks (lower highs/lows) show higher win rate than non-penalized. May be a buy signal not a penalty. Needs multi-date confirmation.

**Idea 11:** Cap Q1+Q2+Q3 combined at 14 pts max — prevents growth-heavy stocks with zero quality metrics from inflating into 80+ artificially. Flag for multi-date testing.

**Idea 12 [HOLD]:** Require Q18 (Financial Strength) ≥ 1 for SA 80+ stocks — filters weak foundation stocks scoring high purely on growth. Not triggered on Oct 20 data (all 80+ already had Q18≥1). Needs dates where NVDA-type pattern appears.

**Idea 13 [HOLD]:** Balance penalty — deduct points when Q1+Q2+Q3 ≥ 12 BUT Q13+Q14+Q18 = 0. Growth/quality divergence = overextended entry risk. Too many winner casualties on Oct 20 — needs multi-date testing.

**Idea 14 [HOLD]:** Investigate Q23 (High P/E Trap) inversion — 7-date analysis shows penalized stocks had 84% WR vs 69% baseline. Contradicted by Oct 20 (0% WR for penalized). Conflicting — needs more dates before acting.

**Idea 15 [HOLD]:** Split Q26 (Sudden Drop) caution tier (-1) into a buy signal — 7-date analysis shows caution tier had 80% WR. Oct 20 shows 0% WR. Directly conflicting — needs more dates.

-----

**Note:** Keep your eyes open to find a flawless overextension sign without filtering breakouts

-----

## Key Findings (2 dates analyzed)

**What's working:**
- 80+ group beats QQQ on both dates (+0.7% in bull, +6.4% in crash)
- Score separation is real — higher groups outperform lower groups consistently
- The one bad month (Feb 14) was a 2.7% frequency crash event — MC's job, not SA's

**What's not working:**
- Q20 (Sector Pref) and Q15 (Country) — strongest negative 'r' across all groups. Hurting the score
- Q1 (Sales Growth) — negative in every group. Rewarding the wrong stocks
- Q27, Q28 — dead weight. Zero variance
- SA total 'r' is only +0.029. Weak. Individual questions pulling in opposite directions

**Biggest opportunities:**
1. Fix or flip Q20, Q15, Q1 — actively making scores worse
2. Remove Q27, Q28 — no signal
3. Strengthen weight on Q4 (Profit Margin), Q3 (CF Growth), Q10 (Debt/Equity) — consistently positive 'r' in 75+ group
4. Exit rules — <70 cutoff is cleanest for crash protection

## After Filters — Oct 20 Results

|Group    |Before|  |After Filters|  |Alpha vs QQQ|
|---------|------|--|-------------|--|------------|
|         |Win%  |N |Win%         |N |            |
|**80+**  |50%   |10|**100%**     |6 |**+14.5%**  |
|**70-80**|39%   |28|**50%**      |14|**+2.7%**   |
|**60-70**|31%   |32|**53%**      |17|**+2.1%**   |
|50-60    |19%   |28|21%          |14|-6.3%       |
|<50      |17%   |6 |—            |— |—           |

**Filters applied:** BM exclusion (max 1) + crypto exclusion + score drop ≥6pts exit + unprofitable (Q4=0) exclusion + P/E >150 exclusion.

## Repeating Winners — Across All Dates

|Symbol  |Sep 11  |Oct 20  |Jan 8   |
|--------|--------|--------|--------|
|**MU**  |✅ +20.6%|✅ +9.3% |✅ +20.7%|
|**LRCX**|✅ +13.7%|✅ +3.3% |✅ +14.9%|
|**KLAC**|✅ +2.5% |✅ +1.3% |✅ +8.9% |
|**WDC** |✅ +20.0%|✅ +26.7%|✅ +50.6%|
|PLTR    |✅ +6.7% |❌ -8.9% |—       |

MU, LRCX, KLAC, WDC — **winners every date they appeared.**

-----

*Updated: Feb 18, 2026*

---
---
---


















