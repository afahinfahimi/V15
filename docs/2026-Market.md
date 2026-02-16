# Module2 - Market

**Lovable Trade V17 — Updated 2/16/2026**

## Market Condition (MC) Score Overview
- Run before stock recommendations.
- Data source: Connected FMP API.

**Missing Data Handling:** If data unavailable for any question:
- Calculate midpoint: (Max + Min) ÷ 2, round toward zero.
- Examples: Q1 (-5 to +5) → 0 pts.

---

## Required Data Dictionary (Source: FMP API)

The app must fetch the following data points for the calculation.


| Ticker | Data Points Needed |
|--------|--------------------|
| **QQQ** | Price, 20D SMA, 50D SMA, 200D SMA, RSI (14), Bollinger Bands (20, 2), MACD (12, 26, 9), 52-Week High, 52-Week Low, 52-Week %Chg, 1D %Chg, 5D %Chg, 10D %Chg, 20D %Chg, 1M %Chg, 2M %Chg, 3M %Chg |
| **SPY** | Price, 1D %Chg, 5D %Chg, 10D %Chg, 20D %Chg, 1M %Chg, 2M %Chg, 3M %Chg |
| **^VIX** | Price (Level), 3M %Chg |
| **IWM** | 1M %Chg |
| **RSP** | 1M %Chg |
| **EFA** | 1M %Chg |
| **XLY** | 1M %Chg |
| **XLP** | 1M %Chg |
| **XLE** | 1M %Chg |
| **GLD** | 1M %Chg |
| **HYG** | 1M %Chg |
| **XLI** | 1M %Chg |
| **UUP** | 1M %Chg |

### Derived / Calculated Data Requirements

The app must calculate these values using the API data:

1. **MACD (for QQQ):**
   - **Fetch:** MACD (12, 26, 9).
   - **Calculate Direction:** Compare Current Histogram vs Previous Day's Histogram.
   - *Strengthening* = Current > Previous.
   - *Weakening* = Current < Previous.

2. **Bollinger Bands Position (for QQQ):**
   - **Fetch:** Upper Band, Lower Band (20, 2).
   - **Calculate %B:** `(Price - Lower Band) / (Upper Band - Lower Band)`

---

## MC Score Questions

### Technical Indicators 

**Q1: QQQ Price vs Moving Averages (Contrarian)**

- Fields: QQQ Price, 50D SMA, 200D SMA
- Max: +5 / Min: -5

| Position | Points | Interpretation |
|----------|--------|----------------|
| QQQ below both 50MA and 200MA | +5 | Oversold — Buy Opportunity |
| QQQ below 200MA only (above 50MA) | +2 | Partial Oversold |
| QQQ at/near both MAs (±1%) | 0 | Neutral |
| QQQ above 200MA only (below 50MA) | -2 | Partial Extended |
| QQQ above both 50MA and 200MA | -5 | Extended — Caution |

---

**Q2: Trend Confirmation (50MA–200MA Spread)**

- Fields: QQQ 50D SMA, QQQ 200D SMA
- Max: +5 / Min: -3

| Condition | Points | Interpretation |
|-----------|--------|----------------|
| MAs flat or converging (±1%) | +5 | Inflection Point |
| 50MA below 200MA, <5% spread | +3 | Early Recovery |
| 50MA above 200MA, <5% spread | +1 | Mild Uptrend |
| 50MA ≥5% above 200MA | -3 | Extended Uptrend |
| 50MA ≥5% below 200MA | -3 | Deep Downtrend |

---

**Q3: RSI Momentum**

- Field: QQQ RSI (14)
- Max: +5 / Min: -3

| RSI | Points | Interpretation |
|-----|--------|----------------|
| < 30 | +5 | Oversold Opportunity |
| 30–40 | +1 | Low Momentum |
| 40–50 | +2 | Below Average |
| 50–60 | +3 | Healthy |
| 60–70 | +1 | Above Average |
| > 70 | -3 | Overbought Risk |

---

**Q4: VIX vs 3-Month Average (Contrarian)**

- Field: VIX 3-Month Price Change %
- Max: +5 / Min: -5

| VIX 3M %Chg | Points | Interpretation |
|--------------|--------|----------------|
| > +20% | +5 | VIX spiking — Buy Opportunity |
| +10% to +20% | +3 | VIX rising |
| -10% to +10% | 0 | VIX near avg |
| -20% to -10% | -3 | VIX falling |
| < -20% | -5 | VIX well below avg — Complacency |

---

**Q5: VIX Absolute Level (Contrarian)**

- Field: VIX Current Price
- Max: +5 / Min: -3

| VIX Level | Points | Interpretation |
|-----------|--------|----------------|
| > 22 | +5 | High Fear — Buy Opportunity |
| 14–22 | 0 | Normal Range |
| < 14 | -3 | Complacency — Caution |

---

**Q6: QQQ 52-Week Strength (Contrarian)**

- Field: QQQ 52-Week Price Change %
- Max: +5 / Min: -3

| 52W Change | Points | Interpretation |
|------------|--------|----------------|
| -10% to 0% | +5 | Slight Loss — Best Buy Opportunity |
| 0% to 10% | +3 | Slight Gain — Early Recovery |
| -20% to -10% | +1 | Moderate Loss |
| < -20% | 0 | Severe Loss |
| 10% to 20% | 0 | Good Yearly Gain |
| ≥ 20% | -3 | Strong Yearly Gain — Extended |

---

**Q7: MACD Score (Contrarian)**

- Fields: MACD Histogram (12, 26, 9)
- Max: +3 / Min: -3
- **Logic:**
  - **Signal:** Positive Histogram = Buy. Negative Histogram = Sell.
  - **Direction:** Compare Current Histogram vs Previous Day.
    - *Strengthening Buy:* Positive and Growing (Current > Previous).
    - *Weakening Buy:* Positive and Shrinking (Current < Previous).
    - *Strengthening Sell:* Negative and Growing (Current < Previous).
    - *Weakening Sell:* Negative and Shrinking (Current > Previous).

| Signal | Direction | Points |
|--------|-----------|--------|
| Sell | Strengthening | +3 |
| Sell | Weakening | +3 |
| Sell | Weakest (Near Zero) | 0 |
| Buy | Weakest (Near Zero) | 0 |
| Buy | Weakening | -3 |
| Buy | Strengthening | -3 |

---

**Q8: QQQ Range Position**

- Fields: QQQ Price, QQQ 52-Week High, QQQ 52-Week Low
- Max: +3 / Min: -2
- **Formula:** `(Price - 52W Low) / (52W High - 52W Low)`

| Range Position | Points | Interpretation |
|----------------|--------|----------------|
| < 0.10 (Bottom 10%) | +3 | Near Support — Bounce Potential |
| 0.10–0.45 | +1 | Lower Half |
| 0.45–0.55 | 0 | Near Middle |
| 0.55–0.90 | +1 | Upper Half |
| > 0.90 (Top 10%) | -2 | Near Resistance — Extended |

---

### Macro Indicators (Q9–Q10)

**Q9: GDP Growth Proxy (Contrarian — SPY 3M %Chg)**

- Field: SPY 3-Month Price Change %
- Max: +3 / Min: -3

| SPY 3M %Chg | Points | Interpretation |
|--------------|--------|----------------|
| Negative | +3 | Contraction — Buy Opportunity |
| 0–6% | 0 | Moderate / Stalling |
| > 6% | -3 | Strong Growth — Extended |

---

**Q10: Unemployment Trend (Contrarian — XLI Momentum)**

- Field: XLI 1-Month Price Change %
- Max: +2 / Min: -1

| XLI 1M %Chg | Points | Interpretation |
|--------------|--------|----------------|
| < -2% | +2 | Industrial Weakness — Buy Opportunity |
| -2% to +3% | 0 | Stable |
| > +3% | -1 | Industrial Expansion — Extended |

---

### Breadth & Intermarket 

**Q11: International Markets (EFA vs SPY)**

- Max: +2 / Min: -2
- **Calculate:** (EFA 1M %Chg) minus (SPY 1M %Chg)

| Spread | Points | Interpretation |
|--------|--------|----------------|
| EFA beats SPY by 1–3% | +2 | Slight Outperformance |
| EFA beats SPY by > 3% | +1 | Global Strength |
| Within ±1% | 0 | Neutral |
| EFA lags SPY by 1–3% | -1 | Slight Underperformance |
| EFA lags SPY by > 3% | -2 | US Isolation |

---

**Q12: Commodities Trend (Contrarian — XLE + GLD)**

- Max: +2 / Min: -2
- **Fields:** XLE 1M %Chg, GLD 1M %Chg
- **Definitions:** "Up" = > +3%. "Down" = < -3%. "Stable" = Between -3% and +3%.

| Condition | Points | Interpretation |
|-----------|--------|----------------|
| XLE Down AND GLD Up | +2 | Risk-Off / Fear — Buy Opportunity |
| XLE Down AND GLD Stable | +1 | Mild Risk-Off |
| Mixed / Both Stable | 0 | Neutral |
| XLE Up AND GLD Stable | -1 | Mild Risk-On |
| XLE Up AND GLD Down | -2 | Risk-On — Extended |

---

**Q13: Small-Cap Breadth (IWM vs QQQ — Reduced Weight)**

- Max: +1 / Min: -1
- **Calculate:** (IWM 1M %Chg) minus (QQQ 1M %Chg)

| Spread | Points | Interpretation |
|--------|--------|----------------|
| IWM beats QQQ by > 3% | +1 | High Risk Appetite |
| Within ±3% | 0 | Neutral |
| IWM lags QQQ by > 3% | -1 | Flight to Safety |

---

**Q14: Sector Rotation (Contrarian — XLY vs XLP)**

- Max: +1 / Min: -1
- **Calculate:** (XLY 1M %Chg) minus (XLP 1M %Chg)

| Spread | Points | Interpretation |
|--------|--------|----------------|
| XLY lags XLP by > 3% | +1 | Defensive Rotation — Buy Opportunity |
| Within ±3% | 0 | Neutral |
| XLY beats XLP by > 3% | -1 | Consumer Confidence — Extended |

---

**Q15: Equal-Weight Breadth (Contrarian — RSP vs SPY)**

- Max: +1 / Min: -1
- **Calculate:** (RSP 1M %Chg) minus (SPY 1M %Chg)

| Spread | Points | Interpretation |
|--------|--------|----------------|
| RSP lags SPY by > 0.5% | +1 | Narrow Rally — Reversal Risk |
| Within ±0.5%  | 0  | Neutral |
| RSP beats SPY by 0.5% to 2% | 0  | Mild Breadth — Neutral |
| RSP beats SPY by > 2% | -1 | Broad Participation — Extended |

---

### Credit & Sentiment 

**Q16: Credit Spreads (Contrarian — HYG Performance)**

- Field: HYG 1-Month Price Change %
- Max: +2 / Min: 0

| HYG 1M %Chg | Points | Interpretation |
|--------------|--------|----------------|
| < -1% | +2 | Credit Stress — Buy Opportunity |
| ≥ -1% | 0 | No Signal |

---

**Q17: Dollar Index (UUP Trend)**

- Field: UUP 1-Month Price Change %
- Max: +2 / Min: -2

| UUP 1M %Chg | Points | Interpretation |
|--------------|--------|----------------|
| < -2% | +2 | Dollar Weakening — Bullish for Equities |
| -2% to 0% | +1 | Mild Weakening |
| 0% to +1% | 0 | Neutral |
| +1% to +2% | -1 | Mild Strengthening |
| > +2% | -2 | Dollar Spiking — Headwind |

---

## Final Score Calculation

- **Raw Score** = Sum of all question points

| Question | Max | Min |
|----------|-----|-----|
| **TOTAL** | **+52** | **-40** |

- **Total Span:** 92 points

**Normalization Formula:**
`((Raw Score - Min) / (Max - Min)) × 100`

*Current equivalent:* `((Raw Score + 40) / 92) * 100`

---

**End of Market Condition Test**


