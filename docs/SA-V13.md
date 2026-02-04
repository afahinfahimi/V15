# STOCK ANALYSIS SCORING SYSTEM (SA TEST)

**Lovable Trade V13 - Updated 2/1/2026**

---

## Stock Score Overview

- Score each stock provided by the user (via CSV or direct entry).
- Answer each question below and find the points that apply.
- Add all points to calculate the Raw Score and normalize it using the formula at the end of this document.
- Pull the data from connected FMP API. 

**Missing Data Handling:** If data unavailable for any question, do not estimate, follow the rules to assign points:
- For questions with positive-only answers (e.g., 0 to 6): assign middle value (e.g., 3)
- For questions with negative-only answer range: assign 0
- For questions with both positive and negative answer range (e.g., -5 to +5): assign 0
- For penalty questions (max 0): assign 0 (no penalty)

---

## Stock Analysis Score Questions 

### Growth Metrics 

**Q1: Sales Growth**
- Fields: Annual Revenue Growth %, Quarterly Revenue Growth % (YoY)
- Max Points: 6
- Min Point: 0

| Points | Condition | Interpretation |
|--------|-----------|----------------|
| 6 | Annual ≥50% AND Quarterly > Annual | Accelerating Exceptional |
| 5 | Annual ≥50% AND Quarterly > 0 but ≤ Annual | Steady Exceptional |
| 5 | Annual 20-49% AND Quarterly > Annual | Accelerating Strong |
| 4 | Annual 20-49% AND Quarterly > 0 but ≤ Annual | Steady Strong |
| 2 | Annual 1-19% AND Quarterly > 0 | Growing |
| 1 | Annual ≥0 AND Quarterly ≤ 0 | Stalling |
| 1 | Annual < 0 AND Quarterly > 0 | Recovering |
| 0 | Annual < 0 AND Quarterly ≤ 0 | Declining |

**Q2: Operating Income Growth**
- Fields: Annual Operating Income Growth %, Quarterly Operating Income Growth % (YoY)
- Max Points: 6
- Min Point: 0

| Points | Condition | Interpretation |
|--------|-----------|----------------|
| 6 | Annual ≥50% AND Quarterly > Annual | Accelerating Exceptional |
| 5 | Annual ≥50% AND Quarterly > 0 but ≤ Annual | Steady Exceptional |
| 5 | Annual 20-49% AND Quarterly > Annual | Accelerating Strong |
| 4 | Annual 20-49% AND Quarterly > 0 but ≤ Annual | Steady Strong |
| 2 | Annual 1-19% AND Quarterly > 0 | Growing |
| 1 | Annual ≥0 AND Quarterly ≤ 0 | Stalling |
| 1 | Annual < 0 AND Quarterly > 0 | Recovering |
| 0 | Annual < 0 AND Quarterly ≤ 0 | Declining |

**Q3: Cash Flow Growth**
- Fields: Annual Operating Cash Flow Growth %, Quarterly Operating Cash Flow Growth % (YoY)
- Max Points: 6
- Min Point: 0

| Points | Condition | Interpretation |
|--------|-----------|----------------|
| 6 | Annual ≥50% AND Quarterly > Annual | Accelerating Exceptional |
| 5 | Annual ≥50% AND Quarterly > 0 but ≤ Annual | Steady Exceptional |
| 5 | Annual 20-49% AND Quarterly > Annual | Accelerating Strong |
| 4 | Annual 20-49% AND Quarterly > 0 but ≤ Annual | Steady Strong |
| 2 | Annual 1-19% AND Quarterly > 0 | Growing |
| 1 | Annual ≥0 AND Quarterly ≤ 0 | Stalling |
| 1 | Annual < 0 AND Quarterly > 0 | Recovering |
| 0 | Annual < 0 AND Quarterly ≤ 0 | Declining |

### Cyclical Sector Cap

**Applies to Sectors:** Basic Materials, Energy, Mining

**Rule:** For stocks in these sectors, the maximum score for **Q1, Q2, and Q3** is capped at **4 points** each (instead of 6).

**Exception (Breakout Rule):**
If **Q23 (Range Position)** equals **"Breakout"** (Score 4), the cap is lifted, and the stock may earn the full 6 points for growth.

---

### Profitability

**Q4: Net Profit Margin**
- Field: Net Profit Margin
- `Net Profit Margin` = Net Income ÷ Revenue (TTM).
- Max Points: 5
- Min Point: 0

| Condition | Points | Interpretation |
|-----------|--------|----------------|
| ≥30% | 5 | Exceptional |
| >20% | 4 | Strong |
| >15% | 3 | Good |
| >10% | 2 | Moderate |
| >5% | 1 | Low |
| ≤5% | 0 | None |

---

### Momentum 

**Q5: 1-Month Price Change**
- Field: 1-Month Price Change %
- Max Points: 4
- Min Point: 0

| Change | Points |
|--------|--------|
| ≥20% | 4 |
| >10% | 3 |
| >5% | 2 |
| >0% | 1 |
| ≤0% | 0 |

**Q6: 10-Day Price Change**
- Field: 10-Day Price Change %
- Max Points: 3
- Min Point: 0

| Change | Points |
|--------|--------|
| ≥15% | 3 |
| >10% | 2 |
| >5% | 1 |
| ≤5% | 0 |

---

### Liquidity 

**Q7: 20-Day Average Volume**
- Field: 20-Day Average Volume
- Max Points: 3
- Min Point: 0

| Volume | Points |
|--------|--------|
| >1M | 3 |
| >500K | 2 |
| >200K | 1 |
| ≤200K | 0 |

---

### Analyst & Institutions

**Q8: Ownership & Coverage**
- Field: Number of Analyst Ratings, Institutional Ownership %
- Conditional Points
- Max Points: 2
- Min Point: 0

| Condition | Points |
|-----------|--------|
| Institutional Ownership % > 0% | +1 |
| Number of Analyst Ratings >0 | +1 |

---

### Technical 

**Q9: Price vs Moving Averages**
- Field: Current Price, 50-Day SMA, 200-Day SMA
- Max Points: 4
- Min Point: 0

| Position | Points |
|----------|--------|
| Above both 50D & 200D | 4 |
| Above 50D only | 3 |
| Above 200D only | 2 |
| Below both | 0 |

---

### Financial Health 

**Q10: Debt-to-Equity Ratio**
- Field: Total Debt / Total Equity (Most Recent Quarter)
- Max Points: 3
- Min Point: -3
- **Evaluate in order listed (stop at first match):**

| D/E Ratio | Points |
|-----------|--------|
| Negative (negative equity) | -3 |
| <0.5 | 3 |
| <1.0 | 2 |
| <2.0 | 1 |
| ≥2.0 | 0 |

---

### Weighted Momentum ⭐ BEST PREDICTOR

**Q11: Momentum Magnitude**
- Field: 52-Week Price Change %, 3-Month Price Change %
- Formula: 52W %Chg + 3M %Chg
- Max Points: 3
- Min Point: 0

| Sum | Points | Backtest Win Rate |
|-----|--------|-------------------|
| >150% | 3 | 73% |
| >100% | 2 | 63% |
| >50% | 1 | 55% |
| ≤50% | 0 | 47% |

---

### EPS Growth

**Q12: EPS Growth Prior Year**
- Field: EPS Growth % (Prior Fiscal Year)
- EPS Growth % (Prior Fiscal Year)
- Max Points: 4
- Min Point: 0

| Growth | Points |
|--------|--------|
| ≥100% | 4 |
| >50% | 3 |
| >25% | 2 |
| >0% | 1 |
| ≤0% | 0 |

---

### Returns 

**Q13: Return on Equity (ROE)**
- Field: Return on Equity (TTM)
- Max Points: 2
- Min Point: 0

| ROE | Points |
|-----|--------|
| >20% | 2 |
| >10% | 1 |
| ≤10% | 0 |

**Q14: Return on Assets (ROA)**
- Field: Return on Assets (TTM)
- Max Points: 3
- Min Point: 0

| ROA | Points |
|-----|--------|
| ≥15% | 3 |
| >10% | 2 |
| >5% | 1 |
| ≤5% | 0 |

---

### Options & Country 

**Q15: Options Available**
- Field: Is Optionable (Boolean)
- Max Points: 1
- Min Point: 0

| Options | Points |
|---------|--------|
| Yes | 1 |
| No | 0 |

**Q16: Country**
- Field: Country
- Max Points: 1
- Min Point: 0

| Country | Points |
|---------|--------|
| USA | +1 |
| Other | 0 |

---

### Risk Penalties

**Q17: Cash Burn Risk**
- Fields: Net Profit Margin, Operating Cash Flow (Quarterly), Market Cap
- Max Points: 0
- Min Point: -3

| Condition | Points |
|-----------|--------|
| Profitable (Net Profit Margin ≥ 0%) | 0 |
| Unprofitable BUT Operating Cash Flow (Quarterly) ≥ 0 | 0 |
| Unprofitable + Negative CF BUT Market Cap ≥ $10B | 0 |
| Profit% < 0% AND Operating Cash Flow (Quarterly) < 0 AND Market Cap < $10B | -3 |

**Note:** FMP API provides the full raw number (e.g., 10,000,000,000 for $10B). *Note: Do not treat as thousands.*

**Q18: Deterioration Risk**
- Fields: Quarterly Revenue, Quarterly Operating Income, Quarterly Operating Cash Flow (vs Same Qtr Year Ago)
- Max Points: 0
- Min Point: -3
- Trigger: Quarterly Revenue < Sales(q-4) AND Quarterly Operating Income < OpIncome(q-4) AND Quarterly Operating Cash Flow < CashFlow(q-4)
- Points: -3

**Combined Penalty Cap:** If both questions trigger, apply -4 total (not cumulative)

---

### Trend & Strength 

**Q19: Trend Consistency**
- Fields: 3-Month Price Change %, 52-Week Price Change %
- Max Points: 3
- Min Point: 0

| Condition | Points |
|-----------|--------|
| Both 3-Month Price Change % AND 52-Week Price Change % positive | 3 |
| One positive, one negative/zero | 1 |
| Both negative or zero | 0 |

**Q20: Financial Strength**
- Fields: Net Profit Margin, Return on Equity, Debt/Equity Ratio
- Max Points: 3
- Min Point: 0

| Condition | Points |
|-----------|--------|
| Net Profit Margin ≥20% AND Return on Equity ≥20% AND Debt/Equity Ratio <0.5 | 3 |
| Net Profit Margin ≥10% AND Return on Equity ≥10% AND Debt/Equity Ratio <1.5 | 2 |
| Net Profit Margin ≥0% AND Return on Equity ≥5% AND Debt/Equity Ratio <3.0 | 1 |
| Fails thresholds or missing data | 0 |

---

### Risk Detection

**Q21: Momentum Divergence Penalty**
- Fields: 52-Week Price Change %, 3-Month Price Change %
- Max Points: 0
- Min Point: -10
- Trigger: 52-Week Price Change % >40% AND 3-Month Price Change % <-5%
- Points: -10


**Q22: Sector Preference**
- Field: Sector (GICS)
- Max Points: 2
- Min Point: -4

| Sector | Points |
|--------|--------|
| Computers and Technology, Finance, Business Services, Aerospace, Defence | +2 |
| Consumer Staples, Consumer Discretionary, Auto-Tires-Trucks, Retail-Wholesale, Medical, Industrial Products, Transportation, Utilities, Construction | +1 |
| Oils-Energy, Basic Materials, Gold/Mining, Pharma | 0 |
| Crypto-related (Bitcoin miners, crypto exchanges, blockchain companies) | -4 |

**Sector Classification Notes:**
- **Gold/Mining includes:** NEM, GFI, KGC, AU, HMY, CDE, HL, NGD, CGAU, AEM, GOLD, AG
- **Crypto includes:** IREN, MARA, CLSK, RIOT, BITF, WULF, HUT, CIFR, COIN, MSTR, CORZ, BTBT, HIVE, BTDR

---

### Range Position

**Q23: %B Position (Bollinger Bands)**
- Field: Bollinger Bands %B (Period 20, StdDev 2)
- Max Points: 4
- Min Point: 0

| Position | Points |
|----------|--------|
| Breakout (>100%) | 4 |
| Buy zone (≤20%) | 3 |
| Mid-range (20-80%) | 2 |
| Upper zone (80-95%) | 1 |
| Near top (>95%) | 0 |

---

### Momentum Quality 

**Q24: Momentum Quality**
- Fields: 52-Week %Chg, 3-Month %Chg, 1-Month %Chg, 10-Day %Chg
- Max Points: 2
- Min Point: -1
- **Evaluate in order listed (stop at first match):**

| Pattern | Condition | Points |
|---------|-----------|--------|
| Spike Risk | 10-Day %Chg >20% AND 1-Month %Chg <10% | -1 |
| Smooth | 52-Week %Chg >0 AND 3-Month %Chg >0 AND 1-Month %Chg >0 AND 52-Week %Chg > 3-Month %Chg > 1-Month %Chg > 10-Day %Chg  | +2 |   
| Accelerating | 3-Month %Chg >0 AND (3-Month %Chg × 4) > 52-Week %Chg | +1 |
| Other | Default | 0 |

---

### Risk Detection - Part 2 

**Q25: High P/E Momentum Trap**
- Fields: P/E Ratio (TTM), 10-Day Price Change %
- Max Points: 0
- Min Point: -4
- Trigger: P/E Ratio (TTM) > 50 AND 10-Day Price Change < -5%
- Points: -4

---

**Q26: Biotech Binary Event Burn** — Max: 0 / Min: -3
- Fields: Sector, 10-Day Price Change %
- Applies to: Healthcare (Biotech, Medical Devices, Drug Manufacturers)
- Trigger: 10D > 15% → Deduct -3 points

---

### Q27: Sustained Downtrend
- Fields: 1-Day Price Change %, 5-Day Price Change %, 1-Month Price Change %
- **Max:** 0 / **Min:** -3
- **Logic:** Check 1D, 5D, and 1M price changes. If ALL THREE are negative, deduct -3 points.
- **Conditions:**
  - 1D < 0% AND 5D < 0% AND 1M < 0%: **-3**
  - Otherwise: **0**
 

---

### Q28: Sudden Drop / Slide
- Fields: Historical Daily Close Prices (last 5 trading days)
- **Category:** Risk Penalty
- **Max:** 0 / **Min:** -6
- **Logic:** Check for abnormal price drops. Evaluate each of the last 3 trading days individually (close-to-close). Also check cumulative 5-day return. Triggered by the HIGHEST matching tier only (no stacking):

  | Tier | Condition | Penalty |
  |------|-----------|---------|
  | Critical | Any single day ≥ 15% drop within last 3 days | -6 |
  | Severe | Any single day ≥ 10% drop within last 3 days | -2 |
  | Caution | Any single day ≥ 7% drop within last 3 days, OR cumulative 5-day return ≤ -10% | -1 |
  | None | No conditions met | 0 |

- **Data Source:** Historical daily prices (last 5 trading days, close prices)
- **Self-Healing:** Clears once the drop day falls outside the 3-day window. If decline continues, the 5-day cumulative condition sustains the Caution tier.
- **Rationale:** Tiered response to abnormal drops. 7%+ warrants caution. 10%+ signals something significant. 15%+ is catastrophic — effectively makes the stock untradeable by score alone.

---

### Q29: Short Interest Risk
- Fields: Short % of Float
- Category: Risk Penalty
- Max: 0 / Min: -1
- Logic: High short interest amplifies volatility in both directions.
  | Short % Float | Penalty |
  |---------------|---------|
  | > 20% | -1 |
  | ≤ 20% | 0 |

- Data Source: FMP institutional/short interest endpoint
- Rationale: Heavily shorted stocks have unpredictable, amplified moves. For 1-month swing trades, this adds risk regardless of direction.

---

### Q30: Low Float Risk
- Fields: Float Shares
- Category: Risk Penalty
- Max: 0 / Min: -1
- Logic: Low float stocks have amplified price swings on normal volume.
  | Float | Penalty |
  |-------|---------|
  | < 20M shares | -1 |
  | ≥ 20M shares | 0 |
- Data Source: FMP company profile (float shares)
- Rationale: Minor risk factor. Low float amplifies volatility but isn't inherently negative.

---

### Q31: Sector Performance vs Peers
- Fields: 1-Month Price Change %, Sector Average 1-Month Return %
- Category: Momentum
- Max: +1 / Min: -1
- Logic: Compare stock's 1M return vs its sector average 1M return.
  | Condition | Points |
  |-----------|--------|
  | Outperforms sector by > 10% | +1 |
  | Within ±10% of sector | 0 |
  | Underperforms sector by > 10% | -1 |
- Data Source: FMP sector performance + stock quote
- Rationale: Relative strength vs peers indicates stock-specific quality beyond market/sector movement.

---

## Final Score Calculation
- **Raw Score** = Sum of assigned points from all questions
**Current Config Reference:**
- **Max Raw Score:** 71 points
- **Min Raw Score:** -42 points
- **Span:** 113 points
**Normalization Formula (auto-calculated from config):**
`((Raw Score - Min) / (Max - Min)) × 100`
*Current equivalent:* `((Raw Score + 42) / 113) × 100`


---

**End of Stock Analysis Test Instructions**

---

