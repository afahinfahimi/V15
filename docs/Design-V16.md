# Rules & Visual Layer V15
**Lovable Trade — "The Skin"**
**Updated: February 13, 2026**

---

## Purpose

This file is the **single source of truth** for all visual mappings.
It takes Signal Keys from the Signal Engine and returns colors, labels, and display properties.
No calculation logic lives here — only lookups and display rules.

All components (Dashboard, Matrix, Market History) call `getVisualsForSignal(key)` from the theme engine, which reads from this file.

---

## SECTION 1: SIGNAL KEY → VISUAL MAP

**The master lookup. Every signal key maps to exactly one row.**

| Signal Key | Display Name | Intent | Color Token (Vibrant) | Icon |
|------------|-------------|--------|----------------------|------|
| B1 | Aggressive Buy | VIX/Fear Buy | --green-2 | 🟢 |
| B2 | Standard Buy | Normal Zone Buy | --teal-2 | 🟢 |
| B3 | Watchlist | Quality stock, wrong timing | --blue-2 | 👁️ |
| H1 | Hold Strong | SA ≥ 75, No Stop | --green-2 | 💪 |
| H2 | Hold Caution | SA 65–75, 10% Stop | --yellow-2 | ⚠️ |
| H3 | Hold Tight | SA 55–65, 5% Stop | --orange-2 | ⛔ |
| S1 | Sell Now | Immediate forced sell | --red-2 | 🔴 |
| S2 | Sell Managed | Stop-based exit | --orange-2 | 🔴 |
| S3 | Sell Consider | Discretionary exit | --yellow-2 | 🟡 |
| W1 | Wait | No buys allowed | --blue-2 | ⏸️ |
| X1 | Emergency Cash | All Cash / Kill Switch | --red-2 | 🛑 |

**Theme Engine Function:**
```
getVisualsForSignal(key) → { displayName, intent, colorToken, icon }
```

---

## SECTION 2: SA SCORE COLORS

Used for SA score circles, badges, matrix cells, and tier badges.

| SA Range | Color Token (Vibrant) | Color Token (Smooth) | Tier Label |
|----------|----------------------|---------------------|------------|
| 75–100 | --green-2 | --green-1 | T1 · Best |
| 65–74 | --yellow-2 | --yellow-1 | T2 · Good |
| 55–64 | --orange-2 | --orange-1 | T3 · Speculative |
| 35–54 | --red-2 | --red-1 | SELL |
| 0–34 | --red-2 | --red-1 | SELL |

**Theme Engine Function:**
```
getColorForSA(score) → { vibrant, smooth, tierLabel }
```

---

## SECTION 3: MC SCORE COLORS & ZONE LABELS

Used for MC score display, progress bars, zone labels, and matrix cells.

| MC Range | Zone Label | Color Token (Vibrant) | Color Token (Smooth) |
|----------|-----------|----------------------|---------------------|
| ≥ 70 | Euphoria | --orange-2 | --orange-1 |
| 55–69 | Extended | --yellow-2 | --yellow-1 |
| 30–54 | Normal | --green-2 | --green-1 |
| 10–29 | Fear | --teal-2 | --teal-1 |
| < 10 | Panic | --red-2 | --red-1 |

**Theme Engine Function:**
```
getColorForMC(score) → { vibrant, smooth, zoneLabel }
```

---

## SECTION 4: ALERT DISPLAY COLORS

Alerts are non-scoring. Same icon shape, different color. Click opens detail toggle.

### Red Alerts (Risk)

| Alert Code | Label | Trigger Summary |
|-----------|-------|-----------------|
| SEC | SEC | Active SEC investigation (Kill Switch) |
| COURT | Court | Lawsuit, hearing, ruling |
| GUIDE_DOWN | Guide ↓ | Forward guidance lowered |
| EPS_DOWN | EPS ↓ | EPS estimates cut > 5% (90d) |
| DOWNGRADE | Downgrade | Net analyst downgrades (30d) |
| INSIDER_SELL | Insider ↓ | Net insider selling (6mo) |

**Color Token:** --red-2

### Yellow Alerts (Caution)

| Alert Code | Label | Trigger Summary |
|-----------|-------|-----------------|
| EARNING | Earning | Earnings within 14 days |
| IPO | IPO | Listed < 12 months |
| TURTLE | Turtle | 52W ±10% AND 1M ±10% |
| LOTTO | Lotto | SA < 35 AND MCap < $500M |

**Color Token:** --yellow-2

### Green Alerts (Opportunity)

| Alert Code | Label | Trigger Summary |
|-----------|-------|-----------------|
| BOUNCE | Bounce | SA < 35 AND 1M < -15% |
| JUMP | Jump | SA < 35 AND 1M > +15% |
| GUIDE_UP | Guide ↑ | Forward guidance raised |
| EPS_UP | EPS ↑ | EPS estimates raised > 5% (90d) |
| UPGRADE | Upgrade | Net analyst upgrades (30d) |
| INSIDER_BUY | Insider ↑ | Net insider buying (6mo) |

**Color Token:** --green-2

**Theme Engine Function:**
```
getColorForAlert(alertCode) → { color, label }
```

---

## SECTION 5: PRICE CHANGE COLORS

Used across all pages for Day, 5D, 1M, 3M, 1Y percentage columns.

| Condition | Color Token |
|-----------|------------|
| > 0% | --green-2 |
| < 0% | --red-2 |
| = 0% | --dark-1 |

---

## SECTION 6: MATRIX DISPLAY RULES

### Score Change Bars

| Condition | Color Token |
|-----------|------------|
| Score increased vs prior day | --green-2 |
| Score decreased vs prior day | --red-2 |
| No change | --dark-1 |

### Matrix Cell Colors

SA cells → Section 2 lookup.
MC cells → Section 3 lookup.

---

## SECTION 7: SCORE BREAKDOWN CARD STYLING

Individual question cards in SA/MC breakdowns.

| Point Value | Border/Text Color |
|-------------|------------------|
| > 0 | --green-2 |
| = 0 | --dark-1 |
| < 0 | --red-2 |

Card style: Transparent background, 0.5px border, colored text for point value.

### Data Source Icons

| Icon | Meaning |
|------|---------|
| 📋 | API — Direct from FMP |
| 📊 | API+Calc — API data with calculation |
| ⊙ | N/A — No data available |
| 🔍 | Proxy — Derived from proxy data |

---

## SECTION 8: EXPANSION BOX STYLING

Stock and MC detail expansion cards.

| Box | Border Token | Background |
|-----|-------------|------------|
| Bulls 🟢 | --blue-2 | --dark-1 |
| Bears 🔴 | --yellow-2 | --dark-1 |
| Buy Ideas | --green-2 | --dark-1 |
| If Owned | --red-2 | --dark-1 |

Border style: `0.25px solid var({token})`

---

## SECTION 9: UI DYNAMIC MAPPINGS

Conditional text coloring based on data state.

| Mapping | Condition | Color Token |
|---------|-----------|------------|
| Profitability | Profitable = Yes | --green-2 |
| Profitability | Profitable = No | --red-2 |
| Momentum | Price > 200DMA | --teal-2 |
| Momentum | Price < 200DMA | --pink-2 |
| Risk | VIX > 25 | --orange-2 |

---

## SECTION 10: CHART SCALING & OVERLAY

### Scaling Logic

Price is the base. Y-axis domain = min/max of price series.

```
scaledValue = minPrice + (value / 100) × priceRange
```

VIX inversion before scaling:
```
invertedVIX = 100 - VIX
```

Amplification (visual only, does not change data):
```
amplifiedValue = midPrice + (scaledValue - midPrice) × amplifyLevel
```
`amplifyLevel` = 1x–4x per series.

Right Y-axis: raw 0–100 scale for reference.

### Chart Line Colors

| Indicator | Line 1 | Line 2 | Line 3 | Line 4 |
|-----------|--------|--------|--------|--------|
| SMA | --blue-2 | --teal-2 | --purple-2 | --orange-2 |
| EMA | --green-2 | --yellow-2 | --pink-2 | --teal-2 |

### Candle Colors

| Direction | Color |
|-----------|-------|
| Up | --green-2 |
| Down | --red-2 |

---

## SECTION 11: COLOR PALETTES

### Vibrant Palette (Primary — signals, charts, logic indicators)

| Name | Hex | CSS Variable |
|------|-----|-------------|
| Green | #00c932 | --green-2 |
| Yellow | #bcae00 | --yellow-2 |
| Red | #f50000 | --red-2 |
| Orange | #f95f00 | --orange-2 |
| Blue | #6eb1ff | --blue-2 |
| Pink | #ec00bf | --pink-2 |
| Purple | #8700ff | --purple-2 |
| Teal | #00aecc | --teal-2 |
| Dark | #1c1c1c | --dark-2 |

### Smooth Palette (Secondary — backgrounds, soft accents)

| Name | Hex | CSS Variable |
|------|-----|-------------|
| Green | #8eff72 | --green-1 |
| Yellow | #fff359 | --yellow-1 |
| Red | #ff5757 | --red-1 |
| Orange | #ff914d | --orange-1 |
| Blue | #5ce7ff | --blue-1 |
| Pink | #ff66e2 | --pink-1 |
| Purple | #b174e7 | --purple-1 |
| Teal | #6eb1ff | --teal-1 |
| Gray | #aaaaaa | --dark-1 |

### Neutral Palette

| Token | Hex | Role |
|-------|-----|------|
| --light-1 | #f2f2f2 | Page background (light) |
| --light-2 | #eae7e8 | Card background |
| --light-3 | #eef1f0 | Secondary background |
| --dark-1 | #aaaaaa | Muted text |
| --dark-2 | #1c1c1c | Strong dark |
| --dark-3 | #3d3e42 | Primary text / borders |

---

## SECTION 12: NUMBER FORMATTING

| Type | Format | Example |
|------|--------|---------|
| Percentages | One decimal | +5.5% |
| Prices | Two decimals | $437.80 |
| Market Cap | Abbreviated | $492.7B |
| Ratios | Two decimals | D/E: 0.21 |

---

## SECTION 13: TYPOGRAPHY RULES

| Element | Weight | Color Source |
|---------|--------|-------------|
| Scores | Bold, inside colored circle | Section 2 or 3 |
| Percentages | Regular | Section 5 |
| Labels | Regular | --dark-3 |

---

## RULE: NO HARDCODED COLORS

All component colors MUST reference CSS variables (`--green-2`, `--blue-1`, etc.) or design system tokens. Hardcoded hex values are **only** permitted in:
- Chart rendering engines (Chart.js, lightweight-charts)
- Design Page palette display

---

**End of Rules & Visual Layer**
