# Module1 - Analysis 

**Lovable Trade V16 - Updated 2/13/2026**

---

## Stock Analysis (SA) Score Overview

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
- Min Points: 0

| Points | Condition | Interpretation |
|--------|-----------|----------------|
| 6 | Annual ≥50% AND Quarterly > Annual | Accelerating Exceptional |
| 5 | Annual ≥50% AND Quarterly > 0 but ≤ Annual | Steady Exceptional |
| 5 | Annual 20-49% AND Quarterly > Annual | Accelerating Strong |
| 4 | Annual 20-49% AND Quarterly > 0 but ≤ Annual | Steady Strong |
| 2 | Annual 1-19% AND Quarterly > 0 | Growing |
| 1 | Annual ≥0 AND Quarterly ≤ 0 | Stalling |
| 1 | Annual <0 AND Quarterly > 0 | Recovering |
| 0 | Annual <0 AND Quarterly ≤ 0 | Declining |

**Q2: Operating Income Growth**
- Fields: Annual Operating Income Growth %, Quarterly Operating Income Growth % (YoY)
- Max Points: 6
- Min Points: 0

| Points | Condition | Interpretation |
|--------|-----------|----------------|
| 6 | Annual ≥50% AND Quarterly > Annual | Accelerating Exceptional |
| 5 | Annual ≥50% AND Quarterly > 0 but ≤ Annual | Steady Exceptional |
| 5 | Annual 20-49% AND Quarterly > Annual | Accelerating Strong |
| 4 | Annual 20-49% AND Quarterly > 0 but ≤ Annual | Steady Strong |
| 2 | Annual 1-19% AND Quarterly > 0 | Growing |
| 1 | Annual ≥0 AND Quarterly ≤ 0 | Stalling |
| 1 | Annual <0 AND Quarterly > 0 | Recovering |
| 0 | Annual <0 AND Quarterly ≤ 0 | Declining |

**Q3: Cash Flow Growth**
- Fields: Annual Operating Cash Flow Growth %, Quarterly Operating Cash Flow Growth % (YoY)
- Max Points: 6
- Min Points: 0

| Points | Condition | Interpretation |
|--------|-----------|----------------|
| 6 | Annual ≥50% AND Quarterly > Annual | Accelerating Exceptional |
| 5 | Annual ≥50% AND Quarterly > 0 but ≤ Annual | Steady Exceptional |
| 5 | Annual 20-49% AND Quarterly > Annual | Accelerating Strong |
| 4 | Annual 20-49% AND Quarterly > 0 but ≤ Annual | Steady Strong |
| 2 | Annual 1-19% AND Quarterly > 0 | Growing |
| 1 | Annual ≥0 AND Quarterly ≤ 0 | Stalling |
| 1 | Annual <0 AND Quarterly > 0 | Recovering |
| 0 | Annual <0 AND Quarterly ≤ 0 | Declining |

### Cyclical Sector Cap

**Applies to Sectors:** Basic Materials, Energy, Mining

**Rule:** For stocks in these sectors, the maximum score for **Q1, Q2, and Q3** is capped at **4 points** each (instead of 6).

**Exception (Breakout Rule):**
If **Q21 (Range Position)** equals **"Breakout"** (Score 4), the cap is lifted, and the stock may earn the full 6 points for growth.

---

### Profitability

**Q4: Net Profit Margin**
- Field: Net Profit Margin
- `Net Profit Margin` = Net Income ÷ Revenue (TTM).
- Max Points: 5
- Min Points: 0

| Condition | Points | Interpretation |
|-----------|--------|----------------|
| ≥30% | 5 | Exceptional |
| ≥20% | 4 | Strong |
| ≥15% | 3 | Good |
| ≥10% | 2 | Moderate |
| ≥5% | 1 | Low |
| <5% | 0 | None |

---

### Momentum 

**Q5: 1-Month Price Change**
- Field: 1-Month Price Change %
- Max Points: 4
- Min Points: 0

| Change | Points |
|--------|--------|
| ≥20% | 4 |
| ≥10% | 3 |
| ≥5% | 2 |
| >0% | 1 |
| ≤0% | 0 |

**Q6: 10-Day Price Change**
- Field: 10-Day Price Change %
- Max Points: 3
- Min Points: 0

| Change | Points |
|--------|--------|
| ≥15% | 3 |
| ≥10% | 2 |
| ≥5% | 1 |
| <5% | 0 |

---

### Liquidity 

**Q7: 20-Day Average Volume**
- Field: 20-Day Average Volume
- Max Points: 3
- Min Points: 0

| Volume | Points |
|--------|--------|
| ≥1M | 3 |
| ≥500K | 2 |
| ≥200K | 1 |
| <200K | 0 |

---

### Analyst & Institutions

**Q8: Ownership & Coverage**
- Field: Number of Analyst Ratings, Institutional Ownership %
- Conditional Points
- Max Points: 2
- Min Points: 0

| Condition | Points |
|-----------|--------|
| Institutional Ownership % > 0% | +1 |
| Number of Analyst Ratings > 0 | +1 |

---

### Technical 

**Q9: Price vs Moving Averages**
- Field: Current Price, 20-Day SMA, 50-Day SMA
- Max Points: 4
- Min Points: 0

| Position | Points |
|----------|--------|
| Above both 20D & 50D | 4 |
| Above 50D only | 3 |
| Above 20D only | 2 |
| Below both | 0 |

---

### Financial Health 

**Q10: Debt-to-Equity Ratio**
- Field: Total Debt / Total Equity (Most Recent Quarter)
- Max Points: 3
- Min Points: -3
- **Evaluate in order listed (stop at first match):**

| D/E Ratio | Points |
|-----------|--------|
| Negative (negative equity) | -3 |
| <0.5 | 3 |
| <1.0 | 2 |
| <2.0 | 1 |
| ≥2.0 | 0 |

---

### Weighted Momentum

**Q11: Momentum Magnitude**
- Field: 52-Week Price Change %, 3-Month Price Change %
- Formula: 52W %Chg + 3M %Chg
- Max Points: 3
- Min Points: 0

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
- Max Points: 4
- Min Points: 0

| Growth | Points |
|--------|--------|
| ≥100% | 4 |
| ≥50% | 3 |
| ≥25% | 2 |
| >0% | 1 |
| ≤0% | 0 |

---

### Returns 

**Q13: Return on Equity (ROE)**
- Field: Return on Equity (TTM)
- Max Points: 2
- Min Points: 0

| ROE | Points |
|-----|--------|
| ≥20% | 2 |
| ≥10% | 1 |
| <10% | 0 |

**Q14: Return on Assets (ROA)**
- Field: Return on Assets (TTM)
- Max Points: 3
- Min Points: 0

| ROA | Points |
|-----|--------|
| ≥15% | 3 |
| ≥10% | 2 |
| ≥5% | 1 |
| <5% | 0 |

---

### Country 

**Q15: Country** 
- Field: Country (from FMP `/api/v3/profile/{symbol}`)
- Max Points: 1
- Min Points: -2

| Tier | Countries | Points |
|------|-----------|--------|
| USA | US-listed domestic companies | +1 |
| Developed | Canada, UK, Netherlands, Germany, France, Japan, Australia, Taiwan, South Korea, Switzerland, Ireland, Israel | 0 |
| China / Emerging Markets | China, Brazil, South Africa, and other EM countries | -2 |

---

### Risk Penalties

**Q16: Cash Burn Risk**
- Fields: Net Profit Margin, Operating Cash Flow (Quarterly), Market Cap
- Max Points: 0
- Min Points: -3

| Condition | Points |
|-----------|--------|
| Profitable (Net Profit Margin ≥ 0%) | 0 |
| Unprofitable BUT Operating Cash Flow (Quarterly) ≥ 0 | 0 |
| Unprofitable + Negative CF BUT Market Cap ≥ $10B | 0 |
| Profit% < 0% AND Operating Cash Flow (Quarterly) < 0 AND Market Cap < $10B | -3 |

**Note:** FMP API provides the full raw number (e.g., 10,000,000,000 for $10B). *Note: Do not treat as thousands.*

**Q17: Deterioration Risk**
- Fields: Quarterly Revenue, Quarterly Operating Income, Quarterly Operating Cash Flow (vs Same Qtr Year Ago)
- Max Points: 0
- Min Points: -3
- Trigger: Quarterly Revenue < Sales(q-4) AND Quarterly Operating Income < OpIncome(q-4) AND Quarterly Operating Cash Flow < CashFlow(q-4)
- Points: -3

**Combined Penalty Cap:** If both Q16 and Q17 trigger, apply -4 total (not cumulative)

---

### Trend & Strength 

**Q18: Financial Strength**
- Fields: Net Profit Margin, Return on Equity, Debt/Equity Ratio
- Max Points: 3
- Min Points: 0

| Condition | Points |
|-----------|--------|
| Net Profit Margin ≥20% AND Return on Equity ≥20% AND Debt/Equity Ratio <0.5 | 3 |
| Net Profit Margin ≥10% AND Return on Equity ≥10% AND Debt/Equity Ratio <1.5 | 2 |
| Net Profit Margin ≥0% AND Return on Equity ≥5% AND Debt/Equity Ratio <3.0 | 1 |
| Fails thresholds or missing data | 0 |

---

### Risk Detection

**Q19: Momentum Divergence Penalty**
- Fields: 52-Week Price Change %, 3-Month Price Change %, 1-Month Price Change %, 10-Day Price Change %
- Max Points: 0
- Min Points: -3
- Trigger: 52-Week Price Change % > 0% AND 3-Month Price Change % < 0% AND 1-Month Price Change % < 0% AND 10-Day Price Change % < 0%
- Points: -3

**Q20: Sector Preference**
- Field: Sector (GICS)
- Max Points: 2
- Min Points: -4

| Sector | Points |
|--------|--------|
| Computers and Technology, Finance, Business Services, Aerospace, Defence | +2 |
| Consumer Staples, Consumer Discretionary, Auto-Tires-Trucks, Retail-Wholesale, Medical, Industrial Products, Transportation, Utilities, Construction | +1 |
| Energy, Basic Materials, Mining, Pharma | 0 |
| Crypto-related (Bitcoin miners, crypto exchanges, blockchain companies) | -4 |

**Sector Classification Notes:**
- **Gold/Mining includes:** NEM, GFI, KGC, AU, HMY, CDE, HL, NGD, CGAU, AEM, GOLD, AG
- **Crypto includes:** IREN, MARA, CLSK, RIOT, BITF, WULF, HUT, CIFR, COIN, MSTR, CORZ, BTBT, HIVE, BTDR

---

### Range Position

**Q21: %B Position (Bollinger Bands — Momentum)**
- Field: Bollinger Bands %B (Period 20, StdDev 2)
- Max Points: 4
- Min Points: 0

| Position | Points |
|----------|--------|
| Breakout (>100%) | 4 |
| Near top (>95%) | 3 |
| Upper zone (80-95%) | 2 |
| Mid-range (20-80%) | 1 |
| Buy zone (≤20%) | 0 |

---

### Momentum Quality 

**Q22: Momentum Quality**
- Fields: 52-Week %Chg, 3-Month %Chg, 1-Month %Chg, 10-Day %Chg
- Max Points: 2
- Min Points: -1
- **Evaluate in order listed (stop at first match):**

| Pattern | Condition | Points |
|---------|-----------|--------|
| Spike Risk | 10-Day %Chg >20% AND 1-Month %Chg <10% | -1 |
| Smooth | 52-Week %Chg >0 AND 3-Month %Chg >0 AND 1-Month %Chg >0 AND 52-Week %Chg > 3-Month %Chg > 1-Month %Chg > 10-Day %Chg | +2 |
| Accelerating | 3-Month %Chg >0 AND (3-Month %Chg × 4) > 52-Week %Chg | +1 |
| Other | Default | 0 |

---

### Risk Detection - Part 2 

**Q23: High P/E Momentum Trap**
- Fields: P/E Ratio (TTM), 10-Day Price Change %
- Max Points: 0
- Min Points: -4
- Trigger: P/E Ratio (TTM) > 50 AND 10-Day Price Change < -5%
- Points: -4

---

**Q24: Biotech Binary Event Burn** 
- Max Points: 0 
- Min Points: -3
- Fields: Sector, 10-Day Price Change %
- Applies to: Medical, Pharma 
- Trigger: 10D > 15% → Deduct -3 points

---

**Q25: Sustained Downtrend**
- Fields: 1-Day Price Change %, 5-Day Price Change %, 1-Month Price Change %
- Max Points: 0 
- Min Points: -3
- **Logic:** Check 1D, 5D, and 1M price changes. If ALL THREE are negative, deduct -3 points.
- **Conditions:**
  - 1D < 0% AND 5D < 0% AND 1M < 0%: **-3**
  - Otherwise: **0**

---

**Q26: Sudden Drop / Slide**
- Fields: Historical Daily Close Prices (last 5 trading days)
- **Category:** Risk Penalty
- Max Points: 0 
- Min Points: -6
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

**Q27: Short Interest Risk**
- Fields: Short % of Float
- Category: Risk Penalty
- Max Points: 0 
- Min Points: -1
- Logic: High short interest amplifies volatility in both directions.

  | Short % Float | Penalty |
  |---------------|---------|
  | > 20% | -1 |
  | ≤ 20% | 0 |

- Data Source: FMP institutional/short interest endpoint
- Rationale: Heavily shorted stocks have unpredictable, amplified moves. For 1-month swing trades, this adds risk regardless of direction.

---

**Q28: Low Float Risk**
- Fields: Float Shares
- Category: Risk Penalty
- Max Points: 0 
- Min Points: -1
- Logic: Low float stocks have amplified price swings on normal volume.

  | Float | Penalty |
  |-------|---------|
  | < 20M shares | -1 |
  | ≥ 20M shares | 0 |

- Data Source: FMP company profile (float shares)
- Rationale: Minor risk factor. Low float amplifies volatility but isn't inherently negative.

---

**Q29: Sector Performance vs Peers**
- Fields: 1-Month Price Change %, Sector Average 1-Month Return %
- Category: Momentum
- Max Points: +1 
- Min Points: -1
- Logic: Compare stock's 1M return vs its sector average 1M return.

  | Condition | Points |
  |-----------|--------|
  | Outperforms sector by > 10% | +1 |
  | Within ±10% of sector | 0 |
  | Underperforms sector by > 10% | -1 |

- Data Source: FMP sector performance + stock quote
- Rationale: Relative strength vs peers indicates stock-specific quality beyond market/sector movement.

---

**Q30: 5-Day Relative Strength (Alpha)**
- Fields: Stock 5-Day Price Change %, QQQ 5-Day Price Change %
- Formula: Alpha = Stock 5D % minus QQQ 5D %
- Category: Momentum
- Max Points: +3 
- Min Points: -3
- **Evaluate in order listed (stop at first match):**

  | Condition | Points |
  |-----------|--------|
  | Stock UP & Market DOWN (true alpha) | +3 |
  | Alpha ≥ +5% (beating market by 5%+) | +2 |
  | Alpha ≥ 0% (beating market) | +1 |
  | Alpha -5% to 0% (slightly lagging) | 0 |
  | Alpha < -5% (badly lagging market) | -2 |
  | Stock DOWN & Market UP (stock-specific problem) | -3 |

- Data Source: FMP quote (stock 5D change) + QQQ quote (market 5D change)
- Rationale: Stock move relative to market isolates stock-specific strength (alpha). Stocks up while market is down had 82.1% win rate. Stocks down while market is up had 47.8% win rate. Combined with Q15 change, improved SA correlation from r=0.152 to r=0.173 (+14%).

---

**Q31: Trend Deterioration (Lower Lows & Lower Highs)**
- Fields: Daily OHLC (last 30 trading days)
- Category: Risk Penalty
- Max Points: 0 
- Min Points: -3

**Calculation:**
1. Find swing highs: days where the high > highs of 2 days before and 2 days after
2. Find swing lows: days where the low < lows of 2 days before and 2 days after
3. Compare last 2-3 swing highs: are they decreasing?
4. Compare last 2-3 swing lows: are they decreasing?
5. Both decreasing → penalty

  | Condition | Points |
  |-----------|--------|
  | Lower highs AND lower lows (2+ swing points each) | -3 |
  | Otherwise | 0 |

- Data Source: FMP `/api/v3/historical-price-full/{symbol}` (daily OHLC)
- Rationale: Stocks making lower lows and lower highs are in a structural downtrend regardless of other indicators. Detects topping patterns that momentum questions miss.

---

## Final Score Calculation
- **Raw Score** = Sum of assigned points from all questions

**Current Config Reference:**
- **Max Raw Score:** 70 points
- **Min Raw Score:** -42 points
- **Span:** 112 points

**Normalization Formula (auto-calculated from config):**
`((Raw Score - Min) / (Max - Min)) × 100`
*Current equivalent:* `((Raw Score + 42) / 112) * 100`

---

## Analysis Scores Color Code / Guide

Use the table to apply colors to text, icons, background, 
borders and shapes that represent Analysis scores.

| Score | Color | 
|-------|-------|
| 80 to 99 | t-green | 
| 70 to 79 | t-teal | 
| 60 to 69 | t-yellow | 
| 50 to 59 | t-orange |  
| 40 to 49 | t-red |  
| < 40 | a-red |  

---

**End of Stock Analysis Test Instructions**

---

## Alerts 

Alerts are **non-scoring** visual indicators that provide context beyond analysis scores. 
They do not modify scores. They appear in different tables, cards and icons based on instructions.
Source from FMP API when available. Yahoo and Google Finance and web search otherwise.
Search the sources above to find any of the situations below, if you do, add the Alert message
from the tables below. Accompany the message with the related color based on instructions.

### Alert Display Rules
The alert columns show the same icon when alert exist. They just show different color. 
Clicking on an alert, opens a toggle box with the content of Alert below.

---

### Alerts Colors


#### Red Alerts (Negative / Risk)
**Color: t-red**

| Alert | Trigger |
|-------|---------|
| SEC Investigation | Active SEC investigation, fraud, restatement |
| Legal Issues | Lawsuit, hearing, investigation, ruling news about the company or top executives |
| Forward Guidance Shows Decline | Forward guidance lowered by the company |
| EPS Expectation Lowered | Consensus EPS estimates cut within the last 90 days |
| Downgraded by Analysts | Analyst downgraded the stock within the last 30 days |
| Insider Selling | Top insider's selling within the last 6 months |


#### Yellow Alerts (Caution / Neutral)
**Color: t-yellow**

| Alert | Trigger |
|-------|---------|
| Earnings Date Close | Earnings date is within the next 14 days |
| IPO (New Company) | Company's IPO was Listed within the last 12 months |


#### Green Alerts (Positive / Opportunity)
**Color: t-green**

| Alert | Trigger |
|-------|---------|
| Positive Guidance | Forward guidance is raised |
| EPS increased | Consensus EPS estimates raised within the last 90 days |
| Upgrade by Analysts | Analysts upgraded the stock within the last month |
| Insider Buying | Top Insiders bought stocks within the last 90 days |


---

**End of Alerts Instructions**



























