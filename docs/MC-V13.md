# MARKET CONDITION TEST (MC SCORE)

**Lovable Trade V13 - Optimized 2/1/2026**

## MC Score Overview
- Run before stock recommendations.
- Data source: Connected FMP API. 

**Missing Data Handling:** If data unavailable for any question:
- Calculate midpoint: (Max + Min) ÷ 2, round toward zero.
- Examples: Q1 (-5 to +5) → 0 pts.
- For skipped questions (Q6): assign 0 pts.

---

## Required Data Dictionary (Source: FMP API)

The app must fetch the following data points for the calculation.

| Ticker | Data Points Needed |
|--------|--------------------|
| **QQQ** | Price, 50D SMA, 200D SMA, RSI (14), 52-Week %Chg, Bollinger Bands (20, 2) |
| **SPY** | Price, 50D SMA, 1-Month %Chg, 3-Month %Chg |
| **^VIX** | Price (Level), 3-Month %Chg |
| **IWM** | 1-Month %Chg |
| **RSP** | 1-Month %Chg |
| **IYT** | 1-Month %Chg |
| **EFA** | 1-Month %Chg |
| **XLY** | 1-Month %Chg |
| **XLP** | 1-Month %Chg |
| **XLE** | 1-Month %Chg |
| **GLD** | 1-Month %Chg |
| **HYG** | 1-Month %Chg |
| **XLI** | 1-Month %Chg |
| **UUP** | 1-Month %Chg |
| **TLT** | 1-Month %Chg (Yield Spread proxy) |
| **IEF** | 1-Month %Chg (Yield Spread proxy) |

### Derived / Calculated Data Requirements
The app must calculate these values using the API data:

1.  **MACD (for QQQ):**
    * **Fetch:** MACD (12, 26, 9).
    * **Calculate Direction:** Compare Current Histogram vs Previous Day's Histogram.
    * *Strengthening* = Current > Previous.
    * *Weakening* = Current < Previous.

2.  **Bollinger Bands Position (for QQQ):**
    * **Fetch:** Upper Band, Lower Band (20, 2).
    * **Calculate %B:** `(Price - Lower Band) / (Upper Band - Lower Band)`

3.  **Yield Curve Proxy:**
    * **Fetch:** 10Y Treasury Rate and 2Y Treasury Rate (preferred).
    * **Alternative:** Use TLT and IEF 1M %Chg as directional proxies if direct rates unavailable.

---

### Technical Indicators 

**Q1: QQQ Price vs Moving Averages**

- Fields: QQQ Price, 50D SMA, 200D SMA
- Max: +5 / Min: -5
- +5: QQQ above both 50MA and 200MA
- +2: QQQ above 200MA only (below 50MA)
- 0: QQQ at/near both MAs (± 1%)
- -2: QQQ below 200MA only (above 50MA)
- -5: QQQ below both 50MA and 200MA

**Q2: Trend Confirmation (50MA-200MA Spread)**

- Fields: QQQ 50D SMA, QQQ 200D SMA
- Max: +5 / Min: -5
- +5: 50MA ≥ 5% above 200MA
- +3: 50MA above 200MA, < 5% spread
- 0: MAs flat or converging (± 1%)
- -3: 50MA below 200MA, < 5% spread
- -5: 50MA ≥ 5% below 200MA

**Q3: RSI Momentum**

- Field: QQQ RSI (14)
- Max: +5 / Min: -3
- +5: RSI < 30 (oversold opportunity)
- +3: RSI 50-60
- +2: RSI 40-50
- +1: RSI 60-70 OR RSI 30-40
- -3: RSI > 70 (overbought risk)

**Q4: VIX vs 3-Month Average**

- Field: VIX 3-Month Price Change %
- Max: +5 / Min: -5
- Use VIX 3M %Chg as directional proxy:

| VIX 3M %Chg | Points | Interpretation |
|-------------|--------|----------------|
| < -20% | +5 | VIX well below avg (bullish) |
| -20% to -10% | +3 | VIX below avg |
| -10% to +10% | 0 | VIX near avg |
| +10% to +20% | -3 | VIX above avg |
| > +20% | -5 | VIX well above avg (bearish) |

**Q5: VIX Absolute Level**

- Field: VIX Current Price
- Max: +3 / Min: -5
- +3: VIX 14-18
- +2: VIX < 14
- +1: VIX 18-22
- -1: VIX 22-25
- -3: VIX 25-30
- -5: VIX > 30

**Q6: Intermarket Correlation**
- **Skip this question.** Assign 0 points.

**Q7: QQQ 52-Week Strength**

- Field: QQQ 52-Week Price Change %
- Max: +5 / Min: -5
- +5: Change ≥ 20%
- +3: Change 10-20%
- +1: Change 0-10%
- -1: Change -10% to 0%
- -3: Change -20% to -10%
- -5: Change < -20%

**Q8: MACD Score**

- Max: +4 / Min: -4
- **Fields:** MACD Histogram (12, 26, 9)
- **Logic:**
  - **Signal:** Positive Histogram = Buy. Negative Histogram = Sell.
  - **Direction:** Compare Current Histogram vs Previous Day.
    - *Strengthening Buy:* Positive and Growing (Current > Previous).
    - *Weakening Buy:* Positive and Shrinking (Current < Previous).
    - *Strengthening Sell:* Negative and Growing (Current < Previous).
    - *Weakening Sell:* Negative and Shrinking (Current > Previous).

| Signal | Direction | Points |
|--------|-----------|--------|
| Buy | Strengthening | +4 |
| Buy | Weakening | +2 |
| Buy | Weakest (Near Zero) | +1 |
| Sell | Weakest (Near Zero) | -1 |
| Sell | Weakening | -2 |
| Sell | Strengthening | -4 |

**Q9: QQQ Range Position**

- Max: +3 / Min: -2
- **Fields:** QQQ Price, QQQ 52-Week High, QQQ 52-Week Low
- **Formula:** `(Price - 52W Low) / (52W High - 52W Low)`

| Range Position | Points | Interpretation |
|----------------|--------|----------------|
| < 0.10 (Bottom 10%) | +3 | Near Support (bounce potential) |
| 0.10 - 0.45 | +1 | Lower half |
| 0.45 - 0.55 | 0 | Near middle |
| 0.55 - 0.90 | +1 | Upper half |
| > 0.90 (Top 10%) | -2 | Near Resistance (extended) |

---

### Macro Indicators

**Q10: GDP Growth Proxy (SPY 3M %Chg)**

- Field: SPY 3-Month Price Change %
- Max: +3 / Min: -3
- +3: SPY 3M > 10% (Strong Growth)
- +2: SPY 3M 6-10%
- 0: SPY 3M 3-6%
- -2: SPY 3M 0-3% (Stalling)
- -3: SPY 3M negative (Contraction risk)

**Q11: Federal Reserve Policy (TLT-IEF Spread)**

- Max: +3 / Min: -3
- **Calculate:** (TLT 1-Month %Chg) minus (IEF 1-Month %Chg)
- +3: Spread > 2% (Dovish / Rates Falling)
- +2: Spread 0% to 2%
- 0: Spread -1% to 0% (Stable)
- -1: Spread -2% to -1% (Hawkish lean)
- -3: Spread < -2% (Hawkish / Rates Rising)

**Q12: Unemployment Trend (XLI Momentum)**

- Max: +2 / Min: -2
- Field: XLI 1-Month Price Change %
- +2: Change > 3% (Industrial Expansion)
- +1: Change 0-3%
- 0: Change -2% to 0%
- -1: Change -5% to -2%
- -2: Change < -5% (Industrial Contraction)

**Q13: Treasury Yield Curve**

- Max: +3 / Min: -3
- **Fields:** 10-Year Treasury Rate, 2-Year Treasury Rate
- **Calculate:** (10Y Rate - 2Y Rate)
- +3: Spread > 1% (Healthy)
- +1: Spread 0-1%
- 0: Spread -0.5% to 0% (Flat)
- -2: Spread -1% to -0.5% (Inverted)
- -3: Spread < -1% (Deeply Inverted/Recession Warning)


---

### Breadth & Intermarket 

**Q14: International Markets (EFA vs SPY)**

- Max: +2 / Min: -2
- **Calculate:** (EFA 1M %Chg) minus (SPY 1M %Chg)
- +2: EFA beats SPY by > 3% (Global Strength)
- +1: EFA beats SPY by 1-3%
- 0: Within ± 1%
- -1: EFA lags SPY by 1-3%
- -2: EFA lags SPY by > 3% (US Isolation)

**Q15: Commodities Trend (XLE + GLD)**

- Max: +2 / Min: -2
- **Fields:** XLE 1M %Chg, GLD 1M %Chg
- **Definitions:** "Up" = > +3%. "Down" = < -3%. "Stable" = Between -3% and +3%.
- +2: XLE Up AND GLD Down (Risk-On)
- +1: XLE Up AND GLD Stable
- 0: Mixed / Both Stable
- -1: XLE Down AND GLD Stable
- -2: XLE Down AND GLD Up (Risk-Off/Fear)

**Q16: Small-Cap Breadth (IWM vs QQQ)**

- Max: +3 / Min: -3
- **Calculate:** (IWM 1M %Chg) minus (QQQ 1M %Chg)
- +3: IWM beats QQQ by > 3% (High Risk Appetite)
- +1: IWM beats QQQ by 1-3%
- 0: Within ± 1%
- -1: IWM lags QQQ by 1-3%
- -3: IWM lags QQQ by > 3% (Flight to Safety)

**Q17: Sector Rotation (XLY vs XLP)**

- Max: +2 / Min: -2
- **Calculate:** (XLY 1M %Chg) minus (XLP 1M %Chg)
- +2: XLY beats XLP by > 3% (Consumer Confidence)
- +1: XLY beats XLP by 1-3%
- 0: Within ± 1%
- -1: XLY lags XLP by 1-3%
- -2: XLY lags XLP by > 3% (Defensive Rotation)

**Q18: Equal-Weight Breadth (RSP vs SPY)**

- Max: +2 / Min: -2
- **Calculate:** (RSP 1M %Chg) minus (SPY 1M %Chg)
- +2: RSP beats SPY by > 2% (Broad Participation)
- +1: RSP beats SPY by 0.5-2%
- 0: Within ± 0.5%
- -1: RSP lags SPY by 0.5-2%
- -2: RSP lags SPY by > 2% (Narrow Rally)

**Q19: Transportation (IYT vs SPY)**

- Max: +2 / Min: -2
- **Calculate:** (IYT 1M %Chg) minus (SPY 1M %Chg)
- +2: IYT beats SPY by > 2% (Economic Activity High)
- +1: IYT beats SPY by 0.5-2%
- 0: Within ± 0.5%
- -1: IYT lags SPY by 0.5-2%
- -2: IYT lags SPY by > 2% (Supply Chain/Demand Issues)

---

### Credit & Sentiment 

**Q20: Credit Spreads (HYG Performance)**

- Max: +2 / Min: -2
- Field: HYG 1-Month Price Change %
- +2: Change > 1% (Risk Appetite High)
- +1: Change 0% to 1%
- 0: Change -0.5% to 0%
- -1: Change -1% to -0.5%
- -2: Change < -1% (Credit Stress/Spreads Widening)

**Q21: Dollar Index (UUP Trend)**

- Max: +2 / Min: -2
- Field: UUP 1-Month Price Change %
- +2: Change < -2% (Dollar Weakening - Bullish for Equities)
- +1: Change -2% to 0%
- 0: Change 0% to +1%
- -1: Change +1% to +2%
- -2: Change > +2% (Dollar Spiking - Headwind)

**Q22: Volatility Trend (Secondary VIX Check)**

- Max: +2 / Min: -2
- Field: VIX 1-Month Price Change %
- +2: Change < -10% (Calm)
- +1: Change -5% to -10%
- 0: Change ± 5%
- -1: Change +5% to +10%
- -2: Change > +10% (Rising Fear)

---

## MC Score Modifiers

Apply after base score calculation.

| ID | Modifier | Points | Trigger |
|----|----------|--------|---------|
| M1 | Euphoria Penalty | -5 | QQQ RSI > 75 (Extreme Overbought) |
| M2 | Breadth Divergence | Add Breadth Section total again (doubles breadth penalty) | QQQ Price > 50SMA AND Breadth Section Score < 0 |
| M3 | VIX Divergence | -5 | QQQ Price Up > 1% AND VIX Up > 5% (Fear rising with price) |
| M4 | Yield Curve Risk | -3 | Yield Spread < -0.8% AND QQQ RSI > 70 (ignoring recession risk) |
| M5 | Momentum Fatigue | -3 | MACD Histogram < 0 (Negative) AND QQQ Price > 50SMA (Momentum slowing while price is high) |

---

## Updated Normalization Formula

* **Raw Range:** -93 to +65
* **Total Span:** 158 points

**Normalization Formula (auto-calculated from config):**
`((Raw Score - Min) / (Max - Min)) × 100`

*Current equivalent:* `((Raw Score + 93) / 158) * 100`

---

**End of Market Condition Test Instructions**



















