# RULES & THRESHOLDS (RULES V12)

**Lovable Trade V13 - "Visual Layer" Edition**
**Updated: February 5, 2026**

---

## Overview

This file defines all visual mappings, color tokens, thresholds, and display standards. It is the bridge between scoring (SA/MC/MS) and the UI.

---

Chart Scaling Logic:

Price is the base. The Y-axis domain is defined by the min/max of the price series (stock price or QQQ).
All other series (Score, VIX, MC) are scaled to fit within the price range using: scaledValue = minPrice + (value / 100) * priceRange
VIX is inverted before scaling: (100 - VIX) so high VIX appears low on the chart (bearish = down).
Right Y-axis shows raw 0–100 scale for reference.
Amplification multiplies each series' deviation from the price midpoint: amplifiedValue = midPrice + (scaledValue - midPrice) * amplifyLevel where amplifyLevel is 1x–4x per series.

This scaling ensures all indicators visually overlay on the price, making crossovers and divergences between score trends and price movements immediately visible.

### Notes to undrestand scaling
Scaling — Simple Explanation
The chart needs to show things with different units (dollars, scores, VIX level) on the same chart. So it picks the stock price as the "ruler" and maps everything else onto it.
Formula: scaledValue = minPrice + (value / 100) × priceRange

minPrice — lowest stock price on screen (the bottom of the chart)
priceRange — the gap between the highest and lowest price on screen
value — any 0–100 number (SA score, MC score, or inverted VIX)
scaledValue — where that number gets placed on the chart, expressed in dollars so it lines up with price

VIX inversion: VIX is flipped (100 - VIX) before scaling so that "bad" (high VIX) appears low on the chart and "good" (low VIX) appears high — matching the direction of price.
Amplification: Exaggerates how far a line sits from the middle of the chart. Makes small score changes easier to see. Does not change the data, only the visual.

Your Claim: When you tuned amplification on VIX and price, the two lines crossed at what turned out to be perfect buy/sell points.
The Question: Is this a real tradeable signal, or did the specific amplify settings just happen to make the lines cross at the right moments?
The Test: Strip away all scaling and amplification. Calculate a raw number: (100 - VIX) minus (price's position as a 0–100 percentile in its own range). When this number crosses zero, that's a crossover without any visual tuning. Then check: do those zero-crossings consistently land near reversals across multiple stocks and multiple time periods? If yes — real signal. If it only works on one stock with one amplify setting — coincidence.

---

**Accent Colors:**

yellow-1: #f2f1da (light yellow)
yellow-2: #debe5c (golden)
red-1: #edebea (light red/pink)
red-2: #d5656c (coral red)
blue-1: #eaf9f5 (light cyan)
blue-2: #a6e0da (teal - also primary)
green-1: #e6f5cf (light green)
green-2: #a8fcb9 (bright green)
orange-2: #f69f1c (orange)
Neutrals:

light-1: #f2f2f2 (background)
light-2: #eae7e8 (card)
light-3: #eef1f0 (secondary)
dark-1: #8b9299 (muted text)
dark-2: #717680 (medium gray)
dark-3: #3d3e42 (foreground text)
Special:

icon-on-accent: black (for icons on colored backgrounds)

---

## 2. SA SCORE COLORS

Used for SA score circles, badges, and matrix cells.

| Score Range | Token |
|-------------|-------|
| ≥ 70 | Green-D |
| 65-69 | Green-M |
| 60-64 | Blue-D |
| 55-59 | Blue-M |
| 50-54 | Yellow-M |
| 35-49 | Orange-D |
| < 35 | Red-D |

---

## 3. MC SCORE COLORS & ZONES

Used for MC score display, progress bar, and zone labels.

| MC Score | Token | Zone Label |
|----------|-------|------------|
| ≥ 75 | Red-M | Euphoria |
| 60-74 | Orange-M | Overheated |
| 50-59 | Green-D | Neutral-High |
| 40-49 | Green-M | Neutral-Low |
| 30-39 | Blue-M | Oversold |
| 20-29 | Blue-D | Fear |
| < 20 | Yellow-M | Panic |

**Note:** MC trading actions are defined in MS V12 (Entry Rules). This file defines display only.

---

## 4. MS SIGNAL COLORS

| Signal | Token | Icon |
|--------|-------|------|
| BUY | Green-M | 🟢 |
| SELECTIVE BUY | Yellow-M | 🟡 |
| NO NEW BUYS | Orange-M | ⛔ |
| PANIC | Red-M | 🛑 |
| AVOID | Red-D | ❌ |
| VIX OVERRIDE | Red-L | 🚨 |

---

## 5. AI DOT COLORS

| Dot | Token |
|-----|-------|
| Positive | Green-M |
| Mixed | Yellow-M |
| Negative | Red-M |
| No Data | Gray-D |

---

## 6. TIER BADGE COLORS

| Tier | Token | Display |
|------|-------|---------|
| T1 | Green-L | T1 · $100k |
| T2 | Blue-M | T2 · $50k |
| T3 | Yellow-M | T3 · $20k |
| SELL | Red-M | SELL |

---

## 7. PRICE CHANGE COLORS

Used for Day, 5D, 1M, 3M, 1Y percentage columns across all pages.

| Condition | Token |
|-----------|-------|
| Positive % | Green-D |
| Negative % | Red-D |
| No change / N/A | Gray-D |

---

## 8. EXPANSION BOX STYLING

Stock and MC expansion cards use colored borders.

| Box | Border Token | Background |
|-----|-------------|------------|
| Bulls 🟢 | Blue-L | Gray-XD |
| Bears 🔴 | Yellow-M | Gray-XD |
| Buy Ideas | Green-L | Gray-XD |
| If Owned | Red-M | Gray-XD ||

**Border Style:** `0.25px solid var(--color-{token})`

---

## 9. SCORE BREAKDOWN CARD COLORS

Individual question cards in SA/MC breakdowns.

| Points | Border/Text Token |
|--------|-------------------|
| Positive points | Green-M |
| Zero points | Gray-D |
| Negative points (penalties) | Red-M |

**Card Style:** Transparent background, `0.5px` border, colored text for point value.

---

## 10. MATRIX PAGE COLORS

### Score Bars (Stock Matrix, MC Matrix)

| Condition | Token |
|-----------|-------|
| Score increased vs previous day | Green-M |
| Score decreased vs previous day | Red-M |
| No change | Gray-D |

### Matrix Cell Colors

SA scores in matrix tables follow Section 2 (SA Score Colors).
MC scores in matrix tables follow Section 3 (MC Score Colors).

---

## 11. FLAG COLORS

Defined in FLAGS V12. Reference summary:

| Category | Typical Tokens |
|----------|---------------|
| Kill Switch | Red-D, Navy-D |
| Risk | Red-D, Red-M, Orange-M, Yellow-M |
| Sector | Orange-M, Yellow-D, Brown-M, Purple-D |
| Fundamental (positive) | Green-M |
| Fundamental (negative) | Red-M, Red-L, Pink-M, Brown-M, Navy-M |
| Opportunity | Blue-D, Green-D, Teal-M, Olive-D, Blue-M |

---

## 12. GENERAL DISPLAY STANDARDS

### Typography
- Scores: Bold, inside colored circle
- Percentages: Regular weight, colored by change direction
- Labels: Regular weight, neutral color

### Data Sources Legend (Score Breakdown)
- 📋 API — Direct from FMP API
- 📊 API+Calc — API data with calculation applied
- ⊙ N/A — No data available
- 🔍 Proxy — Derived from proxy data

### Number Formatting
- Percentages: One decimal (e.g., +5.5%)
- Prices: Two decimals (e.g., $437.80)
- Market Cap: Abbreviated (e.g., $492.7B)
- Ratios: Two decimals (e.g., D/E: 0.21)

---

**End of Rules V12**
