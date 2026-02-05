# Lovable Instructions Made by the App Itself V13 Feb 2026

# Stock Analysis Application — Phased Build Specification

## How to Use This Document

This app is built in **6 sequential phases**. Feed each phase to your AI builder (Lovable, Cursor, etc.) one at a time. **Do not proceed to the next phase until the current one works correctly.** Each phase includes acceptance criteria — verify them before moving on.

---

## Global Architecture Rules

These rules apply to ALL phases. Include them in every prompt.

### Technology Stack
- **Frontend**: React 18 + TypeScript + Vite
- **Styling**: Tailwind CSS + shadcn/ui components
- **Backend**: Supabase (Lovable Cloud) — Edge Functions (Deno) for API calls, PostgreSQL for watchlists
- **Data Provider**: Financial Modeling Prep (FMP) API
- **State Management**: React useState/useEffect + React Query

### Mandatory Code Architecture
1. **Separate scoring logic from UI.** All scoring functions live in `src/lib/scoringLogic.ts` (SA) and `src/lib/marketScoring.ts` (MC). Components never contain scoring math.
2. **Data Normalization Layer.** Create `src/lib/dataTransformer.ts` that maps raw FMP API responses into standardized `StockData` and `MarketData` TypeScript interfaces BEFORE data reaches the scoring engine. This prevents silent errors from FMP's inconsistent formats (TTM vs Annual vs Quarterly).
3. **Parallel API calls.** Always use `Promise.all()` when fetching data for multiple symbols. Never make sequential calls.
4. **No database caching.** All analysis is ephemeral — fresh API calls every time.
5. **After-hours data** comes exclusively from FMP's `/stable/aftermarket-quote` endpoint. Do NOT use Yahoo Finance or Google Finance scraping.
6. **Error handling.** Every API call must have try/catch with user-visible error states. If FMP returns null/undefined for a field, follow the missing data rules (defined in Phase 2).

### Color System (Use Throughout)

**Stock Analysis Colors (by Normalized Score):**

| Score Range | Color | Hex |
|---|---|---|
| ≥ 65 | Dark Green | `#047857` |
| ≥ 60 | Green | `#36eda6` |
| ≥ 50 | Blue | `#2563eb` |
| ≥ 40 | Light Blue | `#5ba4d9` |
| ≥ 20 | Yellow | `#f9ce16` |
| ≥ 0 | Red | `#df2215` |
| < 0 | Dark Red | `#8b1a1a` |

**Market Condition Colors (by Zone):**

| Zone | Color | Hex |
|---|---|---|
| Panic (0-34) | Black | `#1a1a1a` |
| Buy (35-45) | Green | `#22c55e` |
| Hold (46-64) | Yellow | `#eab308` |
| Sell (65-100) | Red | `#ef4444` |

---

# PHASE 1: Data Layer + Debug Shell

**Goal:** Build the data fetching infrastructure, normalization layer, and a raw debug UI to verify data is flowing correctly. No scoring yet.

## Prompt for AI Builder:

> Build the data fetching layer for a stock analysis app using Supabase Edge Functions and Financial Modeling Prep (FMP) API. This phase is ONLY about getting data and displaying it raw — no scoring logic yet.

### 1A. TypeScript Interfaces

Create `src/types/stock.ts`:

```typescript
export interface StockData {
  symbol: string;
  name: string;
  sector: string;
  industry: string;
  marketCap: number;
  price: number;
  country: string;
  hasOptions: boolean;
  hasAnalystCoverage: boolean;

  // Price changes
  priceChange1D: number;       // % change today
  priceChange5D: number;       // % change 5 days
  priceChange10D: number;      // % change 10 days
  priceChange1M: number;       // % change 1 month
  priceChange3M: number;       // % change 3 months
  priceChange52W: number;      // % change 52 weeks
  sessionChange0: number;      // Today's intraday %
  sessionChange1: number;      // Yesterday's daily %
  sessionChange2: number;      // 2 days ago daily %

  // Growth (annual = YoY from annual statements; quarterly = QoQ or YoY from quarterly)
  annualRevenueGrowth: number | null;
  quarterlyRevenueGrowth: number | null;
  annualOperatingIncomeGrowth: number | null;
  quarterlyOperatingIncomeGrowth: number | null;
  annualCashFlowGrowth: number | null;
  quarterlyCashFlowGrowth: number | null;

  // Profitability
  netProfitMargin: number | null;
  roe: number | null;
  roa: number | null;
  epsGrowthPriorYear: number | null;

  // Valuation
  peRatio: number | null;
  debtToEquity: number | null;

  // Technical
  sma50: number | null;
  sma200: number | null;
  avgVolume20Day: number | null;
  institutionalOwnership: number | null;
  bollingerUpper: number | null;
  bollingerLower: number | null;

  // Risk
  shortPercentFloat: number | null;
  floatShares: number | null;
  sectorPerformance1M: number | null;

  // Chart data
  priceHistory: Array<{ date: string; open: number; high: number; low: number; close: number; volume: number }>;
}

export interface MarketData {
  date: string;
  qqqPrice: number;
  qqqDayChange: number;
  qqq5DChange: number;
  qqq1MChange: number;
  qqq3MChange: number;
  qqq1YChange: number;
  qqqPriceHistory: Array<{ date: string; open: number; high: number; low: number; close: number }>;
  tqqqPrice: number;
  tqqqDayChange: number;
  benchmark: { symbol: string; price: number; sma50: number; sma200: number };
  indicators: {
    q1_priceVsMa: { price: number; sma50: number; sma200: number };
    q2_maSpread: number;
    q3_rsi: number;
    q4_vix3MonthChange: number;
    q5_vixLevel: number;
    q7_qqq52WeekChange: number;
    q8_macdHistogram: number;
    q8_prevMacdHistogram: number;
    q9_rangePosition: number;
    q10_gdpProxy: number;
    q11_fedPolicy: number;
    q12_unemployment: number;
    q13_yieldCurve: number;
    q14_intlPerf: number;
    q15_gold1M: number;
    q15_energy1M: number;
    q16_smallCapPerf: number;
    q17_sectorRotation: number;
    q18_equalWeight: number;
    q19_transports: number;
    q20_creditSpread: number;
    q21_dollarTrend: number;
    q22_vixTrend: number;
    qqq1DChange: number;
    vix1DChange: number;
    qqqAbove50SMA: boolean;
  };
}
```

### 1B. Data Transformer

Create `src/lib/dataTransformer.ts`:

This module takes raw FMP API responses and maps them to the `StockData` interface. Key responsibilities:
- Compute growth rates from raw financial statement data (annual revenue from two periods → YoY %)
- Handle quarterly operating income: compare current quarter to same quarter last year (YoY), NOT sequential QoQ
- Normalize null/undefined/NaN values to `null`
- Compute 10-day and 5-day price changes from price history when FMP doesn't provide them directly
- Compute session changes (daily % moves for last 3 trading days) from price history

### 1C. Edge Functions

Create two Supabase Edge Functions:

**`fetch-stock-data`** — accepts `{ symbols: string[], date?: string }`, calls these FMP endpoints in parallel per symbol:
- `/stable/profile?symbol=`
- `/stable/quote?symbol=`
- `/stable/ratios-ttm?symbol=`
- `/stable/key-metrics-ttm?symbol=`
- `/stable/income-statement-growth?symbol=&period=annual&limit=2`
- `/stable/income-statement?symbol=&period=quarter&limit=8`
- `/stable/cash-flow-statement?symbol=&period=quarter&limit=8`
- `/stable/cash-flow-statement?symbol=&period=annual&limit=2`
- `/stable/balance-sheet-statement?symbol=&period=annual&limit=2`
- `/stable/historical-price-eod/full?symbol=` (last 260 trading days)
- `/stable/stock-price-change?symbol=`
- `/stable/shares-float?symbol=`
- `/stable/aftermarket-quote?symbol=`
- `/api/v3/technical_indicator/daily/{symbol}?type=bbands&period=20`
- `/api/v3/earning_calendar?symbol=`

Return raw JSON. The transformer handles normalization on the client.

**`fetch-market-data`** — accepts `{ date?: string }`, fetches data for: QQQ, TQQQ, ^VIX (or VIXY), SPY, TLT, IEF, XLI, EFA, XLE, GLD, IWM, XLY, XLP, RSP, IYT, HYG, UUP. Uses the same endpoint pattern. Computes derived indicators (RSI, MACD, MA spreads) server-side.

**Rate limiting:** Implement retry with exponential backoff (max 3 retries, starting at 1s delay).

**Weekend/holiday data filtering:** FMP's `historical-price-eod` returns calendar dates that include weekends and holidays (as gaps). For any logic that needs "last N trading days" (e.g., Q28 Sudden Drop needs 3 trading days), always request at least 5 calendar days of data and filter to rows with valid closing prices. Do NOT assume consecutive calendar days are trading days — a Monday fetch with "last 3 days" will include Saturday/Sunday nulls. The data transformer should expose a `getLastNTradingDays(priceHistory, n)` utility that handles this filtering.

### 1D. Debug UI

Build a minimal page with:
- Text input for comma-separated symbols
- "Fetch" button
- Raw data display: show every field from `StockData` in a table for each symbol
- Highlight any `null` fields in yellow so I can verify data coverage
- Show a separate section for `MarketData` with all indicator values
- Display API response time for each call

### Acceptance Criteria
- [ ] Enter "AAPL, NVDA, MSFT" and see all StockData fields populated (non-null) for each
- [ ] MarketData loads with all indicator values visible
- [ ] Null fields are clearly highlighted
- [ ] Multiple symbols fetch in parallel (not sequential — verify via timing)
- [ ] After-hours data shows when market is closed (from FMP aftermarket endpoint only)

---

# PHASE 2: Stock Analysis Scoring Engine (SA)

**Goal:** Build the 31-question scoring engine as a standalone module with a debug UI showing per-question point breakdowns.

## Prompt for AI Builder:

> Build the Stock Analysis scoring engine. This is the most critical part of the app — accuracy of the 31-question scoring is the #1 priority. Create the scoring logic in a SEPARATE `src/lib/scoringLogic.ts` file. Build a "Debug Mode" UI where I can see exactly how many points were awarded for each question (Q1-Q31) for a specific ticker.

### 2A. Scoring Logic — `src/lib/scoringLogic.ts`

This file exports a single main function:

```typescript
export interface QuestionResult {
  id: string;          // "Q1", "Q2", etc.
  label: string;       // "Sales Growth", "Operating Income Growth", etc.
  points: number;      // Points awarded
  maxPoints: number;   // Maximum possible (positive end of range)
  minPoints: number;   // Minimum possible (negative end of range)
  explanation: string; // Human-readable reason, e.g. "Annual +32%, Quarterly +45% (> Annual) → 5 pts"
  dataUsed: Record<string, number | string | boolean | null>; // Raw inputs for debugging
}

export interface ScoringResult {
  symbol: string;
  rawScore: number;          // Sum of all question points
  normalizedScore: number;   // ((raw + 42) / 95) × 100, capped 0-100
  tier: 'T1' | 'T2' | 'T3' | 'Short' | 'Avoid' | 'ETF';
  questions: QuestionResult[];
  flags: string[];           // Any special flags/alerts
  biotechCapped: boolean;    // Whether Q26 cap was applied
}

export function calculateStockScore(stock: StockData): ScoringResult;
```

### 2B. All 31 Questions — Exact Logic

**IMPORTANT: Implement each question EXACTLY as specified. Do not simplify, combine, or reinterpret the logic.**

#### Growth Metrics (Q1-Q3) — Max 18 pts total (6 pts each)

All three questions use identical threshold logic, applied to different metrics:
- Q1: Sales Growth (annualRevenueGrowth, quarterlyRevenueGrowth)
- Q2: Operating Income Growth (annualOperatingIncomeGrowth, quarterlyOperatingIncomeGrowth)
- Q3: Cash Flow Growth (annualCashFlowGrowth, quarterlyCashFlowGrowth)

```
Scoring (evaluate top-to-bottom, first match wins):
  Annual ≥ 50% AND Quarterly > Annual       → 6 pts
  Annual ≥ 50% AND Quarterly > 0            → 5 pts
  Annual ≥ 20% AND Quarterly > Annual       → 5 pts
  Annual ≥ 20% AND Quarterly > 0            → 4 pts
  Annual ≥ 1%  AND Quarterly > 0            → 2 pts
  Annual ≥ 0%  AND Quarterly ≤ 0            → 1 pt
  Annual < 0%  AND Quarterly > 0            → 1 pt
  Both negative                             → 0 pts
```

#### Profitability (Q4) — Max 5 pts

```
Net Profit Margin:
  ≥ 30%   → 5 pts
  > 20%   → 4 pts
  > 15%   → 3 pts
  > 10%   → 2 pts
  > 5%    → 1 pt
  ≤ 5%    → 0 pts
```

#### Momentum (Q5-Q6) — Max 7 pts

```
Q5: 1-Month Price Change [0-4 pts]
  ≥ 20%  → 4 pts
  > 10%  → 3 pts
  > 5%   → 2 pts
  > 0%   → 1 pt
  ≤ 0%   → 0 pts

Q6: 10-Day Price Change [0-3 pts]
  ≥ 15%  → 3 pts
  > 10%  → 2 pts
  > 5%   → 1 pt
  ≤ 5%   → 0 pts
```

#### Liquidity & Coverage (Q7-Q8) — Max 5 pts

```
Q7: Volume Liquidity [0-3 pts]
  avg20DayVolume > 1,000,000  → 3 pts
  > 500,000                   → 2 pts
  > 200,000                   → 1 pt
  ≤ 200,000                   → 0 pts

Q8: Ownership & Coverage [0-2 pts]
  institutionalOwnership > 0  → +1 pt
  hasAnalystCoverage = true   → +1 pt
```

#### Technical (Q9) — Max 4 pts

```
Price vs Moving Averages:
  Above both 50D & 200D  → 4 pts
  Above 50D only          → 3 pts
  Above 200D only         → 2 pts
  Below both              → 0 pts
```

#### Financial Health (Q10) — Range: -3 to +3

```
Debt-to-Equity:
  Negative D/E (negative shareholder equity)  → -3 pts  ← CHECK THIS FIRST
  < 0.5   → 3 pts
  < 1.0   → 2 pts
  < 2.0   → 1 pt
  ≥ 2.0   → 0 pts
```

#### Weighted Momentum (Q11) — Max 3 pts — BEST PREDICTOR

```
Momentum Magnitude = priceChange52W + priceChange3M
  > 150%  → 3 pts
  > 100%  → 2 pts
  > 50%   → 1 pt
  ≤ 50%   → 0 pts
```

#### EPS & Returns (Q12-Q14) — Max 9 pts

```
Q12: EPS Growth Prior Year [0-4 pts]
  ≥ 50%  → 4 pts
  ≥ 20%  → 3 pts
  ≥ 1%   → 2 pts
  ≥ 0%   → 1 pt
  < 0%   → 0 pts

Q13: Return on Equity [0-2 pts]
  ≥ 20%  → 2 pts
  ≥ 10%  → 1 pt
  < 10%  → 0 pts

Q14: Return on Assets [0-3 pts]
  ≥ 15%  → 3 pts
  ≥ 10%  → 2 pts
  ≥ 5%   → 1 pt
  < 5%   → 0 pts
```

#### Options & Country (Q15-Q16) — Max 2 pts

```
Q15: hasOptions = true  → 1 pt, else 0
Q16: country = "US"     → 1 pt, else 0
```

#### Risk Penalties (Q17-Q18) — Range: -5 to 0 (combined cap)

```
Q17: Cash Burn Risk [-3 to 0]
  netProfitMargin < 0 AND annualCashFlowGrowth < 0 AND marketCap < 10,000,000,000
  → -3 pts
  Otherwise → 0 pts

Q18: Deterioration Risk [-3 to 0]
  quarterlyRevenueGrowth < 0 AND quarterlyOperatingIncomeGrowth < 0 AND quarterlyCashFlowGrowth < 0
  → -3 pts
  Otherwise → 0 pts

IMPORTANT: Combined Q17 + Q18 penalty is CAPPED at -5 total (not -6)
```

#### Trend & Strength (Q19-Q20) — Max 6 pts

```
Q19: Trend Consistency [0-3 pts]
  priceChange3M > 0 AND priceChange52W > 0  → 3 pts
  Either one positive                        → 1 pt
  Both negative                              → 0 pts

Q20: Financial Strength [0-3 pts]
  netProfitMargin ≥ 20% AND roe ≥ 20% AND debtToEquity < 0.5  → 3 pts
  netProfitMargin ≥ 10% AND roe ≥ 10% AND debtToEquity < 1.5  → 2 pts
  netProfitMargin ≥ 0%  AND roe ≥ 5%  AND debtToEquity < 3.0  → 1 pt
  Otherwise                                                     → 0 pts
```

#### Risk Detection (Q21-Q22) — Range: -14 to +2

```
Q21: Momentum Divergence Penalty
  priceChange52W > 40% AND priceChange3M < -5%  → -10 pts
  Otherwise                                       → 0 pts

Q22: Sector Preference [-4 to +2]
  sector in [Technology, Financial Services, Business Services]  → +2 pts
  sector in [Consumer Cyclical, Consumer Defensive, Healthcare, Industrial]  → +1 pt
  sector in [Energy, Basic Materials, Mining]  → 0 pts
  Crypto-related stocks (see list below)      → -4 pts

Crypto list: IREN, MARA, CLSK, RIOT, BITF, WULF, HUT, CIFR, COIN, MSTR, CORZ, BTBT, HIVE, BTDR
```

#### Range Position (Q23) — Max 4 pts

```
Bollinger %B = (price - bollingerLower) / (bollingerUpper - bollingerLower) × 100

  %B > 100%     → 4 pts (Breakout)
  %B ≤ 20%      → 3 pts (Buy zone)
  %B 20-80%     → 2 pts (Mid-range)
  %B 80-95%     → 1 pt  (Upper zone)
  %B > 95%      → 0 pts (Near top)
```

#### Momentum Quality (Q24) — Range: -1 to +2

```
CHECK SPIKE RISK FIRST:
  priceChange10D > 20% AND priceChange1M < 10%  → -1 pt (return immediately)

Then check (first match wins):
  52W > 0 AND 3M > 0 AND 1M > 0 AND 10D > 0 AND 52W > 3M > 1M > 10D  → +2 pts (Smooth)
  priceChange3M > 0 AND (priceChange3M × 4) > priceChange52W            → +1 pt (Accelerating)
  Otherwise → 0 pts
```

#### High P/E Trap (Q25) — Min: -5 pts

```
peRatio > 50 AND priceChange10D < -5%  → -5 pts
Otherwise                               → 0 pts
```

#### Biotech Cap (Q26) — Special Override

```
Applies when: sector is "Healthcare" AND industry contains "Biotechnology" or "Medical Devices"
Trigger: priceChange10D > 15%
Action: After calculating all other questions, cap normalizedScore at maximum 55
```

#### V13 New Penalties (Q27-Q31)

```
Q27: Sustained Downtrend [-3 to 0]
  priceChange1D < 0 AND priceChange5D < 0 AND priceChange1M < 0  → -3 pts

Q28: Sudden Drop [-10 to 0]
  Check sessionChange0, sessionChange1, sessionChange2 (last 3 trading days):
  Any single day ≥ 15% drop  → -10 pts
  Any single day ≥ 10% drop  → -5 pts
  Any single day ≥ 7% drop OR priceChange5D ≤ -10%  → -3 pts
  Use worst (most negative) penalty only. Self-heals when drop exits 3-day window.

Q29: Short Interest Risk [-2 to 0]
  shortPercentFloat > 30%  → -2 pts
  shortPercentFloat > 20%  → -1 pt
  Otherwise → 0 pts

Q30: Low Float Risk [-1 to 0]
  floatShares < 20,000,000  → -1 pt

Q31: Sector vs Peers [-1 to +1]
  (priceChange1M - sectorPerformance1M) > 10%   → +1 pt
  (priceChange1M - sectorPerformance1M) < -10%  → -1 pt
  Otherwise → 0 pts
```

### 2C. Special Handling

#### Normalization
```
rawScore range: -42 to +71 (theoretical)
normalizedScore = ((rawScore + 42) / 95) × 100
Clamp to 0-100
```

#### Cyclical Sector Cap
- Sectors: Basic Materials, Energy, Mining
- Cap growth questions (Q1 + Q2 + Q3) total at 4 points maximum
- EXCEPTION: If Q23 = 4 (Breakout), lift the cap

#### ETF Classification
```
3x Index ETFs (TQQQ, UPRO, SOXL, SPXL, TECL, FAS, LABU, TNA, SQQQ, SPXS, SOXS):
  → Tier: T1 Auto, score: N/A, skip scoring entirely

2x Single Stock ETFs (TSLL→TSLA, NVDL→NVDA, MUU→MU, MSFU→MSFT, AAPB→AAPL, FBL→META, PTIR→PLTR, GGLL→GOOG):
  → Score the underlying stock, display the ETF ticker

All other ETFs:
  → Tier: ETF, score: N/A
```

#### Tier Classification
```
function calculateTier(normalizedScore, profitMargin, marketCap, hasOptions, growthQsWithPoints):
  isProfitable = profitMargin > 0

  if normalizedScore ≥ 55 AND isProfitable AND marketCap > 50B    → T1
  if normalizedScore ≥ 50 AND isProfitable AND marketCap > 2B     → T2
  if normalizedScore ≥ 50 AND marketCap > 100M:
    if !isProfitable AND growthQsWithPoints ≥ 2                   → T3
    if isProfitable AND marketCap ≤ 2B                            → T3
  → Avoid
```

### 2D. Missing Data Rules

When FMP returns null/undefined for a data point:
- Questions with **positive-only range** (e.g., Q1-Q9): Award middle value of the range
- Questions with **negative-only range** (e.g., Q17, Q18): Award 0 pts (no penalty)
- Questions with **both positive and negative** range (e.g., Q10, Q21): Award 0 pts
- **Penalty questions** with missing data: Award 0 pts (no penalty applied)

### 2E. Debug Mode UI

Build a page that shows for each analyzed stock:
- A table with columns: Question ID | Label | Points Awarded | Max | Min | Explanation | Raw Data Used
- Color each row: green if points > 0, red if points < 0, gray if 0
- Show the raw score, normalization calculation, and final normalized score
- Show tier assignment with reasoning
- Show any flags (biotech cap, cyclical cap, ETF classification)
- Show an "Insufficient Data" indicator for any question where data was null

### 2F. Static Test Cases

**CRITICAL:** Before testing with live API data, verify the scoring engine against these hardcoded test objects. The AI builder must produce EXACTLY the expected scores. If any test fails, fix the scoring logic before proceeding.

#### Test Case 1: Perfect T1 Stock

```json
{
  "symbol": "PERFECT_T1",
  "sector": "Technology",
  "industry": "Software",
  "country": "US",
  "marketCap": 200000000000,
  "price": 500,
  "hasOptions": true,
  "hasAnalystCoverage": true,
  "annualRevenueGrowth": 55,
  "quarterlyRevenueGrowth": 60,
  "annualOperatingIncomeGrowth": 55,
  "quarterlyOperatingIncomeGrowth": 60,
  "annualCashFlowGrowth": 55,
  "quarterlyCashFlowGrowth": 60,
  "netProfitMargin": 35,
  "priceChange1D": 1.5,
  "priceChange5D": 3,
  "priceChange10D": 8,
  "priceChange1M": 12,
  "priceChange3M": 40,
  "priceChange52W": 120,
  "sessionChange0": 1.5,
  "sessionChange1": 0.8,
  "sessionChange2": 0.5,
  "avgVolume20Day": 5000000,
  "institutionalOwnership": 75,
  "sma50": 480,
  "sma200": 420,
  "debtToEquity": 0.3,
  "epsGrowthPriorYear": 55,
  "roe": 25,
  "roa": 18,
  "peRatio": 35,
  "bollingerUpper": 510,
  "bollingerLower": 470,
  "shortPercentFloat": 3,
  "floatShares": 500000000,
  "sectorPerformance1M": 5,
  "quarterlyCashFlowGrowth": 60,
  "priceHistory": []
}
```

**Expected Results:**
- Q1: 6, Q2: 6, Q3: 6 (Annual ≥50%, Quarterly > Annual)
- Q4: 5 (margin ≥30%)
- Q5: 3 (1M >10%), Q6: 1 (10D >5%)
- Q7: 3 (vol >1M), Q8: 2 (inst + analyst)
- Q9: 4 (above both MAs)
- Q10: 3 (D/E <0.5)
- Q11: 2 (52W+3M = 160% >150% → 3 pts) — CORRECTION: 120+40=160 → 3 pts
- Q12: 4 (EPS ≥50%), Q13: 2 (ROE ≥20%), Q14: 3 (ROA ≥15%)
- Q15: 1, Q16: 1
- Q17: 0, Q18: 0 (profitable, growing)
- Q19: 3 (both 3M and 52W positive)
- Q20: 3 (margin ≥20%, ROE ≥20%, D/E <0.5)
- Q21: 0 (52W >40% but 3M not <-5%)
- Q22: +2 (Technology)
- Q23: 2 (%B = (500-470)/(510-470) = 75% → Mid-range)
- Q24: 0 (52W > 3M but 3M > 1M > 10D? 40 > 12 > 8 → not all decreasing chain. Check smooth: need 52W>3M>1M>10D all positive: 120>40>12>8 ✓ → +2 pts)
- Q25: 0 (P/E 35, not >50)
- Q26: N/A (not biotech)
- Q27: 0 (1D positive), Q28: 0 (no drops), Q29: 0 (short 3%), Q30: 0 (float 500M), Q31: +1 (outperforming sector by 7% — wait, 12-5=7, not >10 → 0 pts)

**Raw Score: 6+6+6+5+3+1+3+2+4+3+3+4+2+3+1+1+0+0+3+3+0+2+2+2+0+0+0+0+0+0+0 = 65**
**Normalized: ((65+42)/95)×100 = 112.6 → capped at 100**
**Tier: T1** (score ≥55, profitable, cap >$50B)

#### Test Case 2: Borderline T3 — Unprofitable Growth Stock

```json
{
  "symbol": "BORDER_T3",
  "sector": "Technology",
  "industry": "Software - Application",
  "country": "US",
  "marketCap": 5000000000,
  "price": 30,
  "hasOptions": true,
  "hasAnalystCoverage": true,
  "annualRevenueGrowth": 25,
  "quarterlyRevenueGrowth": 30,
  "annualOperatingIncomeGrowth": -10,
  "quarterlyOperatingIncomeGrowth": -5,
  "annualCashFlowGrowth": -15,
  "quarterlyCashFlowGrowth": -8,
  "netProfitMargin": -5,
  "priceChange1D": 0.5,
  "priceChange5D": 2,
  "priceChange10D": 6,
  "priceChange1M": 8,
  "priceChange3M": 15,
  "priceChange52W": 45,
  "sessionChange0": 0.5,
  "sessionChange1": 1.2,
  "sessionChange2": -0.3,
  "avgVolume20Day": 2000000,
  "institutionalOwnership": 40,
  "sma50": 28,
  "sma200": 25,
  "debtToEquity": 0.8,
  "epsGrowthPriorYear": -20,
  "roe": -8,
  "roa": -3,
  "peRatio": null,
  "bollingerUpper": 32,
  "bollingerLower": 27,
  "shortPercentFloat": 12,
  "floatShares": 150000000,
  "sectorPerformance1M": 5,
  "priceHistory": []
}
```

**Expected Results:**
- Q1: 5 (Annual ≥20%, Quarterly > Annual), Q2: 0 (both negative), Q3: 0 (both negative)
- Q4: 0 (margin ≤5%), Q5: 2 (1M >5%), Q6: 1 (10D >5%)
- Q7: 3 (vol >1M), Q8: 2, Q9: 4 (above both MAs)
- Q10: 2 (D/E <1.0), Q11: 1 (52W+3M = 60 >50)
- Q12: 0 (EPS <0%), Q13: 0 (ROE <10%), Q14: 0 (ROA <5%)
- Q15: 1, Q16: 1, Q17: 0 (unprofitable + negative CF, but cap >$10B → no penalty)
- Q18: 0 (only rev is growing, op income and CF declining — but quarterly revenue is positive so not ALL three declining → 0)
- Q19: 3 (both 3M and 52W positive), Q20: 0 (margin <0%)
- Q21: 0 (52W >40% = 45% but 3M = 15%, not <-5%), Q22: +2 (Tech)
- Q23: 2 (%B = (30-27)/(32-27) = 60% → Mid-range)
- Q24: check smooth: 45>15>8>6 all positive, descending ✓ → +2
- Q25: 0 (P/E null), Q26: N/A, Q27: 0 (1D positive)
- Q28: 0, Q29: 0 (short 12%), Q30: 0 (float 150M), Q31: 0 (8-5=3, not >10)

**Raw Score: 5+0+0+0+2+1+3+2+4+2+1+0+0+0+1+1+0+0+3+0+0+2+2+2+0+0+0+0+0+0+0 = 31**
**Normalized: ((31+42)/95)×100 = 76.8**
**Tier: T3** (score ≥50, unprofitable, cap >$100M, growthQsWithPoints=1 — only Q1 scored. Need ≥2 → actually **Avoid**)

*Note: This test case verifies the T3 requirement that unprofitable stocks need ≥2 growth questions with points. With only Q1 scoring, this is Avoid despite the 76.8 normalized score.*

#### Test Case 3: Penalized Biotech

```json
{
  "symbol": "BIOPENALTY",
  "sector": "Healthcare",
  "industry": "Biotechnology",
  "country": "US",
  "marketCap": 3000000000,
  "price": 45,
  "hasOptions": true,
  "hasAnalystCoverage": true,
  "annualRevenueGrowth": 80,
  "quarterlyRevenueGrowth": 90,
  "annualOperatingIncomeGrowth": 60,
  "quarterlyOperatingIncomeGrowth": 70,
  "annualCashFlowGrowth": 40,
  "quarterlyCashFlowGrowth": 50,
  "netProfitMargin": 25,
  "priceChange1D": 5,
  "priceChange5D": 10,
  "priceChange10D": 18,
  "priceChange1M": 25,
  "priceChange3M": 50,
  "priceChange52W": 130,
  "sessionChange0": 5,
  "sessionChange1": 4,
  "sessionChange2": 3,
  "avgVolume20Day": 3000000,
  "institutionalOwnership": 60,
  "sma50": 40,
  "sma200": 32,
  "debtToEquity": 0.4,
  "epsGrowthPriorYear": 80,
  "roe": 22,
  "roa": 16,
  "peRatio": 30,
  "bollingerUpper": 48,
  "bollingerLower": 38,
  "shortPercentFloat": 8,
  "floatShares": 80000000,
  "sectorPerformance1M": 5,
  "priceHistory": []
}
```

**Expected Results:**
- High raw score from strong growth metrics
- Q26 TRIGGERS: Healthcare + Biotechnology + 10D (18%) > 15%
- **Normalized score capped at 55 regardless of raw score**
- Verify the cap is applied AFTER all other scoring

#### Test Case 4: Avoid — Deteriorating Stock with Penalties

```json
{
  "symbol": "AVOID_BAD",
  "sector": "Consumer Cyclical",
  "industry": "Retail",
  "country": "US",
  "marketCap": 800000000,
  "price": 8,
  "hasOptions": true,
  "hasAnalystCoverage": false,
  "annualRevenueGrowth": -15,
  "quarterlyRevenueGrowth": -20,
  "annualOperatingIncomeGrowth": -30,
  "quarterlyOperatingIncomeGrowth": -25,
  "annualCashFlowGrowth": -40,
  "quarterlyCashFlowGrowth": -35,
  "netProfitMargin": -12,
  "priceChange1D": -2,
  "priceChange5D": -8,
  "priceChange10D": -6,
  "priceChange1M": -15,
  "priceChange3M": -25,
  "priceChange52W": 55,
  "sessionChange0": -2,
  "sessionChange1": -3,
  "sessionChange2": -11,
  "avgVolume20Day": 800000,
  "institutionalOwnership": 20,
  "sma50": 10,
  "sma200": 12,
  "debtToEquity": 3.5,
  "epsGrowthPriorYear": -50,
  "roe": -15,
  "roa": -8,
  "peRatio": null,
  "bollingerUpper": 11,
  "bollingerLower": 7,
  "shortPercentFloat": 25,
  "floatShares": 40000000,
  "sectorPerformance1M": -5,
  "priceHistory": []
}
```

**Expected Results:**
- Q1-Q3: 0 each (all negative growth)
- Q4: 0 (margin <0%), Q5: 0 (1M negative), Q6: 0 (10D negative)
- Q9: 0 (below both MAs — price 8, sma50 10, sma200 12)
- Q10: 0 (D/E ≥2.0)
- Q17: -3 (unprofitable + negative CF + cap <$10B)
- Q18: -3 (all three quarterly metrics declining) → combined Q17+Q18 = -6, CAPPED at -5
- Q21: -10 (52W >40% AND 3M <-5%)
- Q22: +1 (Consumer Cyclical)
- Q27: -3 (1D, 5D, 1M all negative)
- Q28: -5 (sessionChange2 = -11%, ≥10% drop)
- Q29: -1 (short >20%)
- **Tier: Avoid** (low score, unprofitable, penalties stacked)

*This test verifies: Q17+Q18 cap at -5, Q21 momentum divergence, Q27 sustained downtrend, Q28 sudden drop detection, Q29 short interest.*

### Acceptance Criteria
- [ ] All 4 static test cases produce exactly the expected scores
- [ ] Analyze AAPL: see all 31 questions with point breakdowns
- [ ] Analyze a biotech stock with 10D > 15%: verify score caps at 55
- [ ] Analyze TQQQ: verify it gets T1 Auto with no scoring
- [ ] Analyze a stock with negative D/E: verify Q10 = -3
- [ ] Verify Q17+Q18 combined penalty never exceeds -5
- [ ] Verify Q28 uses worst single penalty (not cumulative)
- [ ] Compare 3 known stocks against your current app's scores — they should match

---

# PHASE 3: Market Condition Scoring Engine (MC)

**Goal:** Build the 22-question + 5 modifier market scoring engine with its own debug view.

## Prompt for AI Builder:

> Build the Market Condition (MC) scoring engine in `src/lib/marketScoring.ts`. Same pattern as SA — separate logic file, debug UI showing per-question breakdowns. Uses QQQ as primary benchmark.

### 3A. Scoring Logic — `src/lib/marketScoring.ts`

```typescript
export interface MCQuestionResult {
  id: string;          // "Q1", "M1", etc.
  label: string;
  points: number;
  maxPoints: number;
  minPoints: number;
  explanation: string;
  dataUsed: Record<string, number | string | boolean | null>;
}

export interface MarketScoringResult {
  rawScore: number;
  normalizedScore: number;     // ((raw + 93) / 158) × 100
  zone: 'Panic' | 'Buy' | 'Hold' | 'Sell';
  questions: MCQuestionResult[];
  modifiers: MCQuestionResult[];
  sectionScores: {
    technical: number;
    macro: number;
    breadth: number;
    creditSentiment: number;
    modifiers: number;
  };
}

export function calculateMarketScore(data: MarketData): MarketScoringResult;
```

### 3B. All 22 Questions + 5 Modifiers

#### Technical Indicators (Q1-Q9) — Range: -37 to +35

```
Q1: QQQ Price vs Moving Averages [-5 to +5]
  Above both 50MA and 200MA  → +5
  Above 200MA only           → +2
  At/near both MAs (±1%)     → 0
  Below 200MA only           → -2
  Below both                 → -5

Q2: Trend Confirmation — 50MA vs 200MA Spread [-5 to +5]
  50MA ≥ 5% above 200MA     → +5
  50MA above, < 5% spread   → +3
  Flat/converging (±1%)      → 0
  50MA below, < 5% spread   → -3
  50MA ≥ 5% below            → -5

Q3: RSI Momentum [-3 to +5]
  RSI < 30 (oversold)   → +5
  RSI 50-60              → +3
  RSI 40-50              → +2
  RSI 60-70 or 30-40    → +1
  RSI > 70 (overbought) → -3

Q4: VIX vs 3-Month Average [-5 to +5]
  VIX 3M %Chg < -20%         → +5
  VIX 3M %Chg -20% to -10%   → +3
  VIX 3M %Chg ±10%           → 0
  VIX 3M %Chg +10% to +20%   → -3
  VIX 3M %Chg > +20%         → -5

Q5: VIX Absolute Level [-5 to +3]
  VIX 14-18    → +3
  VIX < 14     → +2
  VIX 18-22    → +1
  VIX 22-25    → -1
  VIX 25-30    → -3
  VIX > 30     → -5

Q6: Intermarket Correlation → SKIPPED (always 0 pts)

Q7: QQQ 52-Week Strength [-5 to +5]
  ≥ 20%         → +5
  10% to 20%    → +3
  0% to 10%     → +1
  -10% to 0%    → -1
  -20% to -10%  → -3
  < -20%        → -5

Q8: MACD Score [-4 to +4]
  Buy Signal + Strengthening (histogram > prev)    → +4
  Buy Signal + Weakening                            → +2
  Buy Signal + Near Zero                            → +1
  Sell Signal + Near Zero                           → -1
  Sell Signal + Weakening                           → -2
  Sell Signal + Strengthening (histogram < prev)    → -4

  "Buy Signal" = histogram > 0; "Sell Signal" = histogram < 0
  "Strengthening" = abs(histogram) > abs(prevHistogram)

Q9: QQQ Range Position [-2 to +3]
  Bottom 10%        → +3 (bounce potential)
  Lower half 10-45% → +1
  Middle 45-55%     → 0
  Upper half 55-90% → +1
  Top 10%           → -2 (extended)
```

#### Macro Indicators (Q10-Q13) — Range: -11 to +11

```
Q10: GDP Growth Proxy — SPY 3-Month Change [-3 to +3]
  > 5%     → +3
  > 0%     → +1
  -5% to 0% → -1
  < -5%    → -3

Q11: Federal Reserve Policy — TLT minus IEF 1-Month Performance [-3 to +3]
  Spread > 2%   → +3 (dovish)
  Spread > 0%   → +1
  Spread -2% to 0% → -1
  Spread < -2%  → -3 (hawkish)

Q12: Unemployment Trend — XLI 1-Month Change [-2 to +2]
  > 3%    → +2
  > 0%    → +1
  -3% to 0% → -1
  < -3%   → -2

Q13: Treasury Yield Curve [-3 to +3]
  Yield spread > 0.5%   → +3 (normal)
  Yield spread > 0%     → +1
  Yield spread -0.5% to 0% → -1
  Yield spread < -0.5%  → -3 (inverted)
```

#### Breadth & Intermarket (Q14-Q19) — Range: -13 to +13

```
Q14: International Markets — EFA vs SPY 1-Month [-2 to +2]
  EFA outperforming by > 2%  → +2
  EFA outperforming          → +1
  SPY outperforming by < 2%  → -1
  SPY outperforming by > 2%  → -2

Q15: Commodities Trend — (XLE 1M + GLD 1M) / 2 [-2 to +2]
  Average > 3%   → +2
  Average > 0%   → +1
  Average -3% to 0% → -1
  Average < -3%  → -2

Q16: Small-Cap Breadth — IWM vs QQQ 1-Month [-3 to +3]
  IWM outperforming by > 3%  → +3
  IWM outperforming          → +1
  QQQ outperforming by < 3%  → -1
  QQQ outperforming by > 3%  → -3

Q17: Sector Rotation — XLY vs XLP 1-Month [-2 to +2]
  XLY outperforming by > 2%  → +2
  XLY outperforming          → +1
  XLP outperforming by < 2%  → -1
  XLP outperforming by > 2%  → -2

Q18: Equal-Weight Breadth — RSP vs SPY 1-Month [-2 to +2]
  RSP outperforming by > 1%  → +2
  RSP outperforming          → +1
  SPY outperforming by < 1%  → -1
  SPY outperforming by > 1%  → -2

Q19: Transportation — IYT vs SPY 1-Month [-2 to +2]
  IYT outperforming by > 2%  → +2
  IYT outperforming          → +1
  SPY outperforming by < 2%  → -1
  SPY outperforming by > 2%  → -2
```

#### Credit & Sentiment (Q20-Q22) — Range: -6 to +6

```
Q20: Credit Spreads — HYG 1-Month Performance [-2 to +2]
  > 1%    → +2
  > 0%    → +1
  -1% to 0% → -1
  < -1%   → -2

Q21: Dollar Index — UUP 1-Month Trend [-2 to +2]
  UUP < -1% (dollar weakening)   → +2
  UUP < 0%                        → +1
  UUP 0% to 1%                    → -1
  UUP > 1% (dollar strengthening) → -2

Q22: Volatility Trend — VIX 1-Month Change [-2 to +2]
  VIX dropped > 20%  → +2
  VIX dropped         → +1
  VIX rose < 20%      → -1
  VIX rose > 20%      → -2
```

#### Modifiers (M1-M5) — Range: -26 to 0

```
M1: Euphoria Penalty
  QQQ RSI > 75  → -5 pts

M2: Breadth Divergence
  QQQ Price > 50SMA AND breadthSectionScore < 0  → penalty = -1 × breadthSectionScore
  (breadthSectionScore = sum of Q14 through Q19)

M3: VIX Divergence
  QQQ 1-day change > +1% AND VIX 1-day change > +5%  → -5 pts

M4: Yield Curve Risk
  yieldSpread < -0.8% AND QQQ RSI > 70  → -3 pts

M5: Momentum Fatigue
  MACD Histogram < 0 AND QQQ Price > 50SMA  → -3 pts
```

### 3C. Normalization & Zones

```
rawScore range: -93 to +65
normalizedScore = ((rawScore + 93) / 158) × 100
Clamp to 0-100

Zones:
  0-34:   Panic (Black)  — Stand down, SA unreliable
  35-45:  Buy (Green)    — Aggressive, buy SA ≥ 55 stocks
  46-64:  Hold (Yellow)  — Selective, prefer SA ≥ 65 only
  65-100: Sell (Red)     — No new buys, tighten stops
```

### 3D. Debug Mode UI

Same pattern as Phase 2:
- Table showing all 22 questions + 5 modifiers with point breakdowns
- Section subtotals (Technical, Macro, Breadth, Credit/Sentiment, Modifiers)
- Raw score → normalization calculation → zone assignment
- Color-coded zone indicator

### Acceptance Criteria
- [ ] MC score loads with all 22 questions + 5 modifiers visible
- [ ] Section subtotals match sum of individual questions
- [ ] Zone assignment matches the score thresholds
- [ ] Modifier M2 correctly uses breadth section score (not individual questions)
- [ ] Compare against your current app's MC score — should match within ±2 points

---

# PHASE 4: Master Score + Main Dashboard UI

**Goal:** Combine SA + MC into Master Scores, build the production dashboard UI.

## Prompt for AI Builder:

> Combine the SA and MC scoring engines into Master Scores and build the main dashboard UI. The Debug Mode from phases 2-3 should still be accessible via a toggle.

### 4A. Master Score Logic — `src/lib/masterScore.ts`

Three formulas displayed in the stock detail view:

```typescript
export interface MasterScoreResult {
  formulaA: { score: number; signal: string };
  formulaB: { score: number; signal: string };
  zoneSignal: string;
}

// Formula A: (MC + 3×SA) / 4
Signal thresholds:
  ≥ 70  → BUY
  60-69 → LEAN BUY
  50-59 → HOLD
  40-49 → LEAN SELL
  < 40  → SELL

// Formula B: SA + 0.75 × (100 - MC)    ← This is the MAIN "MS" column
Signal thresholds:
  ≥ 90  → BUY
  75-89 → LEAN BUY
  60-74 → HOLD
  45-59 → LEAN SELL
  < 45  → SELL

// Zone Table (conditional logic, evaluate top-to-bottom):
  VIX ≥ 28                    → "VIX OVERRIDE — Buy QQQ, 50% T1"
  MC < 20                     → SELL
  MC 20-49 AND SA ≥ 55        → BUY
  MC 50-59 AND SA ≥ 65        → LEAN BUY
  MC 60-74                    → LEAN SELL
  MC ≥ 75                     → SELL
```

### 4B. Main Dashboard Layout

```
┌──────────────────────────────────────────────────────────────┐
│ Header: [SA Matrix] [MC Matrix] [Chat] [Settings ▼]         │
├──────────────────────────────────────────────────────────────┤
│ MC Status Bar:                                               │
│   MC Score (color-coded) | VIX Level | QQQ Price | Zone Badge│
│   TQQQ Price | QQQ Day Change                                │
├──────────────────────────────────────────────────────────────┤
│ Input Row:                                                   │
│   [Symbol Input (comma-sep)] [Date Picker] [Watchlist ▼]     │
│   [Analyze Button] [Debug Toggle]                            │
├──────────────────────────────────────────────────────────────┤
│ Results Table (sortable columns):                            │
│ ┌──────┬───────┬──────┬─────┬────┬────┬───────┬────────────┐│
│ │Score │Symbol │Alerts│Tier │MC  │MS  │Price  │1D/5D/1M/3M ││
│ ├──────┼───────┼──────┼─────┼────┼────┼───────┼────────────┤│
│ │ 72   │NVDA   │ ⚠️   │ T1  │ 58 │105 │$890   │+2/+8/+15/+30│
│ │ 65   │AAPL   │      │ T1  │ 58 │ 98 │$182   │+1/+3/+8/+20 │
│ └──────┴───────┴──────┴─────┴────┴────┴───────┴────────────┘│
│                                                              │
│ Expanded Row (click to toggle):                              │
│ ┌────────────────────────────────────────────────────────┐   │
│ │ [Price Chart]  [Question Breakdown]  [Master Scores]   │   │
│ │                                                        │   │
│ │ Price chart: 6-month daily candlestick with 50/200 MA  │   │
│ │                                                        │   │
│ │ Questions: Q1: Sales Growth     +5 pts                 │   │
│ │            Q2: Op Income        +4 pts                 │   │
│ │            ...all 31 questions...                       │   │
│ │                                                        │   │
│ │ Master Score:                                          │   │
│ │   Formula A: 68 (LEAN BUY)                             │   │
│ │   Formula B: 92 (BUY)                                  │   │
│ │   Zone: BUY                                            │   │
│ └────────────────────────────────────────────────────────┘   │
└──────────────────────────────────────────────────────────────┘
```

### 4C. Score Table Features

- **Sortable** by any column (click column header)
- **Score badge**: Color-coded per the SA color system
- **Alerts column**: Show flag icons for biotech cap, cyclical cap, high short interest, sudden drop, VIX override
- **Tier badge**: Styled pill (T1=green, T2=blue, T3=gray, Avoid=red, ETF=purple)
- **MS column**: Uses Formula B value, color-coded by signal
- **Price change columns**: 1D, 5D, 1M, 3M — green for positive, red for negative
- **Expanded row** shows price chart, all 31 question scores, and all 3 master score formulas

### 4D. MC Status Bar

Always visible at top. Shows:
- MC normalized score with zone color background
- VIX level (with ≥28 highlighted in red)
- QQQ current price and day change %
- TQQQ price and day change %
- Trading zone badge (Panic/Buy/Hold/Sell)

### 4E. Position Sizing Display

Show in expanded row based on tier and a configurable portfolio size (default $500K):

| Tier | Max % | Max Amount | High Conviction (SA > 70) |
|---|---|---|---|
| T1 | 10% | $50,000 | 20% / $100,000 |
| T2 | 4% | $20,000 | — |
| T3 | 2% | $10,000 | — |
| Short | 0.4% | $2,000 | — |

### 4F. Error States

- **Symbol not found**: Show "Symbol not found" in red in the results row
- **Partial data**: Show the score but with an "⚠ Incomplete Data" badge; in expanded view, highlight which questions used fallback values
- **API timeout**: Show retry button per symbol
- **Rate limited**: Show "Rate limited — retrying in Xs" with countdown

### Acceptance Criteria
- [ ] Dashboard loads MC bar + symbol input + results table
- [ ] Analyzing 5+ symbols shows parallel loading (not one-by-one)
- [ ] Clicking a row expands to show chart + questions + master scores
- [ ] All 3 master score formulas display with correct signals
- [ ] Columns are sortable
- [ ] Debug toggle switches between production and debug views
- [ ] Error states display correctly for invalid symbols

---

# PHASE 5: Watchlists + Date Picker + Historical Analysis

**Goal:** Add persistence (watchlists), date selection for historical analysis, and trading day validation.

## Prompt for AI Builder:

> Add watchlist management, historical date analysis, and trading day validation to the stock analysis app.

### 5A. Watchlist Management

Database table (Supabase):
```sql
CREATE TABLE watchlists (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name TEXT NOT NULL,
  symbols TEXT[] NOT NULL DEFAULT '{}',
  created_at TIMESTAMPTZ DEFAULT now(),
  updated_at TIMESTAMPTZ DEFAULT now()
);
```

UI:
- Dropdown in header to select a watchlist → populates symbol input
- "Save as Watchlist" button to save current symbols
- Watchlist management modal: create, rename, delete, edit symbols
- Quick "Add to Watchlist" button on each stock row

### 5B. Date Picker

- Calendar date picker next to symbol input
- Default: today (or last trading day if weekend/holiday)
- Selecting a historical date re-runs all analysis using that date's data
- **Trading day validation**: Skip weekends and US market holidays. If user selects a non-trading day, auto-adjust to the most recent prior trading day and show a notice.

### 5C. Analysis Reports (Optional)

```sql
CREATE TABLE analysis_reports (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  analysis_date DATE NOT NULL,
  symbols TEXT[] NOT NULL,
  scores JSONB NOT NULL,
  market_score JSONB,
  created_at TIMESTAMPTZ DEFAULT now()
);
```

- Auto-save each analysis run
- "History" tab showing past reports
- Click a report to reload that analysis

### Acceptance Criteria
- [ ] Create, edit, delete watchlists
- [ ] Selecting a watchlist populates symbols and auto-analyzes
- [ ] Date picker works for historical dates (verify scores differ from today)
- [ ] Weekend dates auto-adjust to Friday
- [ ] Analysis reports save and can be reloaded

---

# PHASE 6: Matrix Views + Polish

**Goal:** Add the matrix (historical comparison) views and final UI polish.

## Prompt for AI Builder:

> Add Score Matrix, Stock Matrix, and Market Matrix views. These show historical score comparisons with forward price returns for backtesting. Also polish the overall UI.

### 6A. Score Matrix

A grid showing: Rows = stocks, Columns = dates (e.g., last 10 trading days). Each cell shows the SA normalized score for that stock on that date, color-coded. This lets you see how scores change over time and whether high scores predicted forward gains.

Additional column: Forward return (price change from analysis date to current date).

### 6B. Stock Matrix

For a single stock: Rows = dates over a time period, Columns = each of the 31 questions. Shows how individual question scores changed over time. Helps identify which factors drove score changes.

### 6C. Market Matrix

Same as Stock Matrix but for MC scores. Rows = dates, Columns = 22 questions + 5 modifiers. Track market regime changes over time.

### 6D. UI Polish

- Responsive layout (works on desktop and tablet)
- Loading skeletons during API calls
- Keyboard shortcuts: Enter to analyze, Escape to collapse rows
- Dark mode support (optional)
- Export results to CSV
- Settings page: FMP API key input, portfolio size for position sizing, default watchlist

### 6E. Navigation

```
Header tabs:
  [SA Analysis]  — Main dashboard (Phase 4)
  [Score Matrix] — Historical score grid
  [Stock Matrix] — Per-stock question timeline
  [MC Matrix]    — Market condition timeline
  [Settings]     — API key, portfolio size, preferences
```

### Acceptance Criteria
- [ ] Score Matrix loads and shows color-coded scores across dates
- [ ] Forward returns column calculates correctly
- [ ] Stock Matrix shows question-level score changes for a single stock
- [ ] MC Matrix shows market condition score changes over time
- [ ] CSV export works
- [ ] Settings persist (stored in Supabase or localStorage)
- [ ] Overall UI is clean, consistent, and responsive

---

## Appendix: Quick Reference

### FMP API Endpoints Used
```
/stable/profile?symbol=
/stable/quote?symbol=
/stable/ratios-ttm?symbol=
/stable/key-metrics-ttm?symbol=
/stable/income-statement-growth?symbol=&period=annual&limit=2
/stable/income-statement?symbol=&period=quarter&limit=8
/stable/cash-flow-statement?symbol=&period=quarter&limit=8
/stable/cash-flow-statement?symbol=&period=annual&limit=2
/stable/balance-sheet-statement?symbol=&period=annual&limit=2
/stable/historical-price-eod/full?symbol=
/stable/stock-price-change?symbol=
/stable/shares-float?symbol=
/stable/aftermarket-quote?symbol=
/api/v3/technical_indicator/daily/{symbol}?type=bbands&period=20
/api/v3/earning_calendar?symbol=
```

### ETF Lists
```
3x Index: TQQQ, UPRO, SOXL, SPXL, TECL, FAS, LABU, TNA, SQQQ, SPXS, SOXS
2x Single Stock: TSLL→TSLA, NVDL→NVDA, MUU→MU, MSFU→MSFT, AAPB→AAPL, FBL→META, PTIR→PLTR, GGLL→GOOG
Crypto-related: IREN, MARA, CLSK, RIOT, BITF, WULF, HUT, CIFR, COIN, MSTR, CORZ, BTBT, HIVE, BTDR
MC Benchmark ETFs: QQQ, TQQQ, ^VIX, SPY, TLT, IEF, XLI, EFA, XLE, GLD, IWM, XLY, XLP, RSP, IYT, HYG, UUP
```

### Score Ranges
```
SA Raw: -42 to +71 → Normalized: ((raw + 42) / 95) × 100 → 0-100
MC Raw: -93 to +65 → Normalized: ((raw + 93) / 158) × 100 → 0-100
```
