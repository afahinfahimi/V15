# RULES & THRESHOLDS (RULES V12)

**Lovable Trade V13 - "Visual Layer" Edition**
**Updated: February 5, 2026**

---

## Overview

This file defines all visual mappings, color tokens, thresholds, and display standards. It is the bridge between scoring (SA/MC/MS) and the UI.


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
