# STOCK ANALYZER & BACKTESTING APP — REPLIT BUILD PROMPT

> **Purpose:** Complete build specification for an AI agent (Replit) to construct from scratch.
> Every detail needed to build the app is in this document.
> Sections are ordered as build dependencies — each section builds on the ones before it.

---

# TABLE OF CONTENTS

1. [Project Overview](#1-project-overview)
2. [Architecture & Tech Stack](#2-architecture--tech-stack)
3. [Database Schema](#3-database-schema)
4. [Design System](#4-design-system)
5. [UX & Interaction Requirements](#5-ux--interaction-requirements)
6. [Page Specifications](#6-page-specifications)
7. [Data Layer & FMP API](#7-data-layer--fmp-api)
8. [Configuration System](#8-configuration-system)
9. [Scoring Engine (Layer 1)](#9-scoring-engine)
10. [Tags & Alerts (Layer 2)](#10-tags--alerts)
11. [AI Assessment (Layer 2)](#11-ai-assessment)
12. [Live Analyzer Dashboard](#12-live-analyzer-dashboard)
13. [AI Chat Assistant](#13-ai-chat-assistant)
14. [Historical Analysis Mode](#14-historical-analysis-mode)
15. [Backtesting Matrices](#15-backtesting-matrices)
16. [Optimization Engine](#16-optimization-engine)
17. [Auto-Optimizer Scaffold (Future)](#17-auto-optimizer-scaffold)
18. [Entry & Exit Optimization (Future)](#18-entry--exit-optimization)

---

# 1. PROJECT OVERVIEW

## Objective

Build a full-stack stock analysis, market scoring, and backtesting application. The system scores individual stocks (SA) and market conditions (MC), combines them into a Master Score (MS), and provides comprehensive backtesting tools to validate and optimize scoring accuracy.

**Primary Goal:** Backtesting and optimization — find the best correlation between scores and forward price movement for swing trading (1-month hold target).

**Secondary Goal:** Live analysis dashboard for daily trading decisions.

## Core Concept

The system replaces subjective trading decisions with a modular, rules-based framework built on three distinct layers:

- **SA Score (Stock Analysis):** Questions scoring individual stock quality (0-100). Evaluates financial health, growth, momentum, risk factors, and sector context. Purely quantitative and data-driven.
- **MC Score (Market Conditions):** Questions scoring the overall market environment (0-100). Combines VIX, breadth, macro, and technical indicators. Higher = better market conditions (default view). A Contrarian view inverts the interpretation.
- **MS (Master Score):** Combines SA + MC into trading decisions. Two configurable formulas run simultaneously for comparison and optimization.

The backtesting engine validates these scores against actual forward price returns across historical dates. The optimization engine identifies the best weights for questions, finds gaps in coverage, and suggests adding, removing, or modifying questions to improve predictive accuracy. An integrated AI chat assistant (Claude API) provides on-demand analysis and optimization discussion with full access to all app data.

## Three-Layer Framework

The entire system is organized into three strictly separated layers. This separation is the foundation of the app — it determines what can be optimized, what provides context, and what drives action.

### Layer 1: SCORES (Quantitative — What We Optimize)

**What it contains:** SA, MC, MS — all config-driven questions, penalties, and modifiers that are data-driven and reproducible from API data.

**Key rule:** ONLY this layer is backtestable and optimizable. If something can be expressed as a number from an API and evaluated historically, it belongs here.

**What lives here:**
- SA questions (Q1-Q31): Growth, valuation, momentum, risk penalties, sector performance
- MC questions: VIX, breadth, macro indicators, technical signals
- MS formulas: Combine SA + MC with configurable expressions and conditional modifiers
- Manual adjustments (per-stock point overrides from `manual-adjustments.md`)

**What does NOT live here:** AI opinions, subjective flags, news sentiment, anything that can't be reproduced from historical data.

**Display:** Colored circles/numbers using the unified 9-tier score color scale.

### Layer 2: ASSESSMENT (Qualitative — What Humans Review)

**What it contains:** Tags, AI research (Bulls/Bears), event alerts, fundamental context — adds information WITHOUT changing scores.

**Key rule:** This layer informs judgment, not formulas. A stock scoring 72 with three red tags is still 72 — but you might not buy it.

**What lives here:**
- Tags: Fundamental (Guide ↑/↓, EPS ↑/↓, Upgrade/Downgrade, Insider +/-), Event (SEC, Court, Earning), Opportunity (Bounce, Jump, Turtle, Lotto, IPO), Risk (Earnings Soon)
- Kill Switch: SEC/Fraud (🚨) — visual warning, doesn't change score
- AI Research: Bulls/Bears analysis, competitive moat, valuation vs peers, upcoming catalysts
- AI Chat: Context-aware discussion integrating all layers

**Display:** Text pill tags (Green-M / Yellow-M / Red-M). Research in expansion cards.

### Layer 3: DECISION (Action — Where Signals Live)

**What it contains:** Trading signals, tiers, risk allocation, entry/exit rules — where scores become actions.

**Key rule:** Signals come from MS ONLY. SA and MC do not issue their own trading signals — they provide quality scores that feed into MS.

**What lives here:**
- MS Signals: BUY, SELECTIVE BUY, NO NEW BUYS, PANIC, AVOID, VIX OVERRIDE (from Zone Table)
- Tiers: T1/T2/T3/S — position sizing based on SA + fundamentals
- Cash allocation: Driven by MC score range + VIX
- Entry/Exit rules: Zone Table conditions, stop losses, profit targets
- Portfolio rules: Sector concentration limits (max 2 per restricted sector)
- Vetoes: Hard caps that override signals (SA and MC vetoes)

**Display:** MS rectangle badge (colored by unified scale), Tier circles, signal icons in MS toggle.

### How the Layers Interact

```
Layer 1 (SCORES)          Layer 2 (ASSESSMENT)        Layer 3 (DECISION)
─────────────────         ──────────────────────      ──────────────────────
SA ──────────────────┐                                
                     ├──→ MS ──────────────────────→  Signals (BUY/SELL/etc.)
MC ──────────────────┘                                Tiers (T1/T2/T3/S)
                          Tags ──→ Human reviews ──→  Cash allocation
                          AI Research                 Entry/Exit rules
                          Bulls/Bears                 Portfolio limits
```

- Scores are calculated first (SA, MC → MS)
- Assessment runs independently (Tags, AI research)
- Decisions are derived from MS scores via the Zone Table
- Assessment doesn't change decisions programmatically — it informs the HUMAN who may override

### Why This Separation Matters

1. **Optimization stays clean.** Only Layer 1 data feeds the optimizer. Mixing AI opinions into scores makes backtesting impossible — you can't reproduce "AI thought this was risky" for a date in 2024.
2. **Tags add context without noise.** A stock with "Insider ↓" tag doesn't get penalized twice if insider selling already correlates with score drops. The tag is for YOU, not the formula.
3. **Signals are centralized.** No conflicting buy/sell signals from SA, MC, and MS. MS resolves everything into one signal per stock.

---

# 2. ARCHITECTURE & TECH STACK

## Stack

- **Frontend:** React + Tailwind CSS (dark theme, #111111 base)
- **Backend:** Python (FastAPI)
- **Database:** PostgreSQL (Replit DB)
- **Data Source:** Financial Modeling Prep (FMP) API — Premium account
- **AI Chat:** Anthropic Claude API
- **Charts:** Recharts or Lightweight Charts (TradingView)
- **Fonts:** Inter for headers/body, Open Sans for tables/data, System Monospace for code

## Architecture Diagram

```
┌───────────────────────────────────────────────────────┐
│                  FRONTEND (React + Tailwind)           │
│                                                       │
│  Analyzer │ Matrices │ Optimizer │ Chat │ Settings     │
└────────────────────────┬──────────────────────────────┘
                         │ REST API + WebSocket (chat)
┌────────────────────────▼──────────────────────────────┐
│                 BACKEND (Python FastAPI)               │
│                                                       │
│  ┌────────────┐ ┌────────────┐ ┌────────────┐        │
│  │  Scoring   │ │  Backtest  │ │  FMP API   │        │
│  │  Engine    │ │  Engine    │ │  Client    │        │
│  └────────────┘ └────────────┘ └────────────┘        │
│  ┌────────────┐ ┌────────────┐ ┌────────────┐        │
│  │ Optimizer  │ │   Config   │ │  Chat/AI   │        │
│  │  Engine    │ │  Manager   │ │  Bridge    │        │
│  └────────────┘ └────────────┘ └────────────┘        │
└────────────────────────┬──────────────────────────────┘
                         │
┌────────────────────────▼──────────────────────────────┐
│               DATABASE (PostgreSQL)                    │
│                                                       │
│  scores │ matrices │ watchlists │ configs │ chat_logs  │
└───────────────────────────────────────────────────────┘
```

## Module Build Order

Build follows the user's actual workflow: score today → score the past → optimize.

### Phase 1: Live Analysis ("Score stocks today and decide what to do")

| Step | Module | Description |
|------|--------|-------------|
| 1 | Data Layer & FMP API | Database setup, API integration, caching |
| 2 | Configuration System | Editable markdown configs + Settings page + General Settings |
| 3 | Scoring Engine | SA + MC + MS scoring from config, including vetoes and penalties |
| 4 | Flags & Alerts | Non-scoring visual indicators integrated with scoring results |
| 5 | AI Assessment | Bulls/Bears, Tags, summary one-liners |
| 6 | Live Analyzer Dashboard | Stock table, MC bar, expansion cards, charts — displays scores + flags + AI together |
| 7 | AI Chat Assistant | Claude API with full context, image/file input, stock discovery |

**Milestone:** User can type tickers, get scored results with AI commentary, discuss trades in chat.

### Phase 2: Historical Analysis & Backtesting ("Score dates in the past")

| Step | Module | Description |
|------|--------|-------------|
| 8 | Historical Analysis Mode | Same Analyzer but for past dates — date picker switches to backtest mode, uses historical data, shows forward returns. Looks like the live Analyzer with minor changes (no AI Assessment, forward return columns added). |
| 9 | Backtesting Matrices | Package historical analyses into structured group views: |
| 9a | — Score Matrix | Multi-stock backtest on a single date with forward returns |
| 9b | — Stock Matrix | Single stock scored over time with price correlation |
| 9c | — MC Matrix | Market conditions over time with QQQ as proxy |
| 10 | Multi-Date Batch Runs | Run same stock list across multiple dates, aggregate into matrix reports |

**Milestone:** User can backtest any date, see forward returns, compare scores to outcomes.

### Phase 3: Optimization ("Make the scoring better")

| Step | Module | Description |
|------|--------|-------------|
| 11 | Optimization Engine (B) | Correlation dashboard, question analysis, weight suggestions |
| 12 | Comparison View | Before/after weight change analysis |
| 13 | Optimization Logging | Run history, config change log |
| 14 | Auto-Optimizer (C) | Scaffold only — inactive for future activation |

**Milestone:** User can identify weak/strong questions, discuss results and suggestions with AI via chat before accepting changes, and track what changed.

### Throughout All Phases
These apply from Step 1 onward and are refined as modules are added:
- Design System (colors, tokens, dark theme)
- Mobile Responsive Layout
- Tooltips & Signal Explanations
- CSV Export
- Symbol Validation & ETF Handling
- State Persistence (back button, calendar memory)

---

# 3. DATABASE SCHEMA

## Core Tables

### `saved_analyses`
Named snapshots of complete analysis results. User can save any analysis run (live or backtest) with a name for future reference.
```
id | name | date | mode (live/backtest) | watchlist_id | watchlist_name
mc_score_id | stock_score_ids (JSON array of score IDs)
notes | created_at | updated_at
```
Example: "Portfolio — Jan 15 2026" saves the full scored result for that watchlist on that date. Reloading it restores the exact table, scores, flags, and expansion data without re-running the analysis.

### `scores`
Stores individual stock analysis results.
```
id | symbol | date | sa_score | sa_raw | mc_score | mc_raw
ms_a | ms_c | tier | vix | price
q1_pts | q1_data | q2_pts | q2_data | ... | q26_pts | q26_data
config_version | created_at
```

### `mc_scores`
Stores market condition results.
```
id | date | mc_score | mc_raw | vix | qqq_price
q1_pts | q1_data | q2_pts | q2_data | ... | q22_pts | q22_data
m1_triggered | m2_triggered | m3_triggered | m4_triggered | m5_triggered
config_version | created_at
```

### `score_matrices`
Links scores to a matrix run for batch operations.
```
id | name | date | watchlist_id | created_at
```

### `score_matrix_entries`
Individual entries in a score matrix.
```
id | matrix_id | score_id | fwd_5d_return | fwd_1m_return | fwd_3m_return
fwd_5d_price | fwd_1m_price | fwd_3m_price
```

### `stock_matrix_entries`
Single stock over time.
```
id | symbol | date | score_id | mc_score_id
fwd_1m_return | fwd_1m_price | created_at
```

### `watchlists`
```
id | name | symbols (JSON array) | created_at | updated_at
```

### `configs`
```
id | filename | content (text) | version | created_at
```

### `config_history`
```
id | config_id | content | version | created_at
```

### `chat_threads`
```
id | title | page_context | context_data (JSON) | created_at | updated_at
```

### `chat_messages`
```
id | thread_id | role (user/assistant) | content | created_at
```

### `optimization_runs` (scaffold)
```
id | date | config_before (JSON) | config_after (JSON)
metrics_before (JSON) | metrics_after (JSON) | status | created_at
```

### `optimization_log`
Records every optimization analysis for history tracking.
```
id | run_date | stock_count | stock_list_name | backtest_dates (JSON array)
config_version | results_summary (JSON) | notes | created_at
```
Results summary JSON: `{ "bucket_70_plus": {"win_rate": 0.80, "avg_return": 12.1, "count": 15}, ... "top_questions": [...], "bottom_questions": [...], "false_positive_rate": 0.12, "suggestions": [...] }`

### `api_cache`
```
id | endpoint | params_hash | response (JSON) | fetched_at | expires_at
```

---

# 4. DESIGN SYSTEM

Dark theme. All colors use tokens — never raw hex in components.

## Color Token Families

### Primary Palette
- **Green:** #00532b (D) / #00814b (M) / #36eda6 (L)
- **Blue:** #002773 (D) / #0074e4 (M) / #62b1ff (L)
- **Teal:** #00516e (D) / #008895 (M) / #09e3cf (L)
- **Yellow:** #ac7600 (D) / #dcc600 (M) / #fff851 (L)
- **Orange:** #d34c00 (D) / #ff6b17 (M) / #ff8537 (L)
- **Red:** #b20010 (D) / #df1515 (M) / #ff7373 (L)
- **Pink:** #70002f (D) / #b70b5d (M) / #ff63a5 (L)
- **Purple:** #5c0e5a (D) / #93378f (M) / #d879ff (L)
- **Brown:** #6e2a20 (D) / #954c00 (M) / #e0b690 (L)
- **Olive:** #4b550c (D) / #8d9e00 (M) / #98bc55 (L)
- **Navy:** #35185e (D) / #624883 (M) / #aba4ff (L)

### Neutral Palette
- Background (Dark): #111111
- Background (Light): #EEEEEE
- Muted Text: #999999
- Toggle Bar: #333333

## Score Color Mappings

### Unified Score Colors (SA, MC, MS)
Same color scale for all scores. Higher = better. 9 tiers.
| Range | Token |
|-------|-------|
| 70+ | Green-L |
| 65-69 | Green-D |
| 60-64 | Teal-D |
| 55-59 | Teal-M |
| 50-54 | Blue-D |
| 40-49 | Blue-M |
| 30-39 | Yellow-M |
| 20-29 | Orange-M |
| < 20 | Red-D |

MC default uses this same scale (higher = greener). The Contrarian "C" circle displays inverted colors with tooltip explanation.

### Price Change & Arrow Colors
- Positive / Up arrow: Green-D
- Flat: Gray-D
- Negative / Down arrow: Red-D

### Tier Badge Colors
| Tier | Token |
|------|-------|
| T1 | Green-L |
| T2 | Blue-M |
| T3 | Yellow-L |
| SELL | Orange-D |

### Tag Colors
| Category | Token |
|----------|-------|
| Positive / Opportunity | Green-M |
| Caution / Neutral | Yellow-M |
| Negative / Risk / Event | Red-M |

### MS Signal Colors
| Signal | Token | Icon |
|--------|-------|------|
| BUY | Green-M | 🟢 |
| SELECTIVE BUY | Yellow-M | 🟡 |
| NO NEW BUYS | Orange-M | ⛔ |
| PANIC | Red-M | 🛑 |
| AVOID | Red-D | ❌ |
| VIX OVERRIDE | Red-L | 🚨 |

### UI Element Colors
| Element | Token |
|---------|-------|
| Buttons / Links | Green-L |
| Manual Adjustment indicator (🔧) | Pink-M |
| Score Breakdown — Positive | Green-M |
| Score Breakdown — Zero | Gray-D |
| Score Breakdown — Negative | Red-M |

## Expansion Card Borders
| Box | Border Token |
|-----|-------------|
| Bulls 🟢 | Blue-L |
| Bears 🔴 | Yellow-M |

## Typography
- Headers/Body: Inter
- Tables/Data: Open Sans
- Code/Config: System Monospace
- Table Headers: 14px | Table Content: 15px min | Symbol Name: 1.1rem | Sector: 12px

## Number Formatting
- Percentages: One decimal (+5.5%)
- Prices: Two decimals ($437.80)
- Market Cap: Abbreviated ($492.7B)
- Ratios: Two decimals (D/E: 0.21)

## Data Source Icons (Score Breakdown)
- 📋 API — Direct from FMP
- 📊 API+Calc — API data with calculation
- ⊙ N/A — No data
- 🔍 Proxy — Derived from proxy data

---

# 5. UX & INTERACTION REQUIREMENTS

These requirements apply across all pages and modules.

---

## 16A. Chart Customization

All chart customization applies to EVERY chart in the app: Analyzer stock charts, QQQ chart, Stock Matrix chart, MC Matrix chart.

### Chart Type Selector
Every data series can be displayed as a different chart type via the chart settings panel (gear icon):

| Chart Type | Available For |
|------------|--------------|
| Candlestick (OHLC) | Price series (stocks, QQQ) |
| Heikin-Ashi | Price series (stocks, QQQ) |
| Line | Any series (prices, scores, VIX, MC) |
| Bar | Scores, VIX, MC, volume |
| Area | Cumulative returns, MC zones |
| Step | Score changes, tier changes |

Example: QQQ as Heikin-Ashi candles, SA scores as bars, VIX as a line, MC as area — all on one chart.

### Candle Timeframes
Price candlestick/Heikin-Ashi charts support multiple timeframes:
- **Daily** (default)
- **4-Hour**
- **1-Hour**

Timeframe selector in chart toolbar. Intraday candles require intraday data from FMP — if unavailable, show daily only with note.

### Score-Based Color Coding Mode

Every chart element representing a scored value (SA, MC, MS, tier) supports **score color coding** — the bar/line/candle color changes dynamically based on the score's value and its color rules from `colors.md`.

| Mode | How It Works |
|------|-------------|
| **Default** | Standard colors (e.g., green up / red down for direction) |
| **Score Color Coding** | Each bar/point uses the score's category color. Example: SA bar at 72 = Green-D, bar at 63 = Blue-D, bar at 48 = Orange-D — color shifts with value on the same chart. |

Applies to:
- SA Score bars → colored by unified score scale (≥70 Green-L, 65-69 Green-D, 60-64 Teal-D, etc.)
- MC Score bars → same unified score scale (default view). Contrarian toggle inverts colors.
- MS Signal indicators → colored by signal (BUY Green-M, SELECTIVE Yellow-M, etc.)
- Tier indicators → colored by tier badge colors

Toggle between Default and Score Color Coding via button in chart toolbar.

### Line & Element Styling

Every chart element's appearance is individually configurable:

| Property | Options |
|----------|---------|
| **Color** | Any token from `colors.md` (e.g., "Red-M", "Blue-L") or custom hex |
| **Line thickness** | Thin (1px), Normal (2px), Thick (3px), Heavy (4px) |
| **Line style** | Solid, Dashed, Dotted |
| **Bar width** | Narrow, Normal, Wide |
| **Opacity** | 25%, 50%, 75%, 100% |

Access via right-click on any chart element → "Style..." or via chart settings panel. Preferences saved per chart view, persist across sessions.

### Chart Navigation & Interaction

| Action | Behavior |
|--------|----------|
| **Horizontal drag** | Pan left/right to see more history or recent data |
| **Scroll wheel** | Zoom in/out — scroll up = fewer days (more detail), scroll down = more days (less detail) |
| **Shift + drag** | Alternative zoom: drag right to zoom in on selection, drag left to zoom out |
| **Pinch (mobile)** | Two-finger pinch to zoom in/out |
| **Double-click** | Reset to default zoom level |
| **Crosshair** | Always active — shows date, price, and all overlay values at cursor position |
| **OHLC tooltip** | Hover on candle → shows Open, High, Low, Close, Volume, and any scored values for that date |

### Chart Element Visibility
Every overlay/indicator has a toggle (eye icon) to show/hide:
- Price line/candles
- Volume bars
- 50 SMA line
- 200 SMA line
- Bollinger Bands
- SA Score bars/line
- MC Score bars/line
- VIX line
- MS-A line
- MS-C line

Toggles persist across sessions.

### SMA Lines
50 SMA and 200 SMA lines available on:
- All individual stock charts (Analyzer expansion cards)
- QQQ chart (MC bar expansion)
- Stock Matrix chart
- MC Matrix chart (QQQ line)
- Score Matrix does not have charts (it's a table)

---

## 16B. Table Interaction

### Sortable Columns
ALL table columns across ALL pages are sortable (click header to sort asc/desc). This includes:
- Analyzer stock table
- Score Matrix
- Stock Matrix
- MC Matrix
- Optimizer tables

### Yesterday's Score Column
- Optional column showing the previous trading day's SA score next to the current score
- Display format: `72 (68)` — current score with yesterday's in parentheses
- **On-demand:** Small refresh icon (🔄) on column header. Clicking fetches previous-day scores.
- This avoids excessive API calls — only loads when user requests it
- Color the delta: green arrow ↑ if improved, red arrow ↓ if declined

### Row Action Icons
Each stock row in the Analyzer table has small icon buttons:

| Icon | Action |
|------|--------|
| 📊 (chart) | Opens inline mini-chart below the row (toggle) |
| 🔢 (breakdown) | Opens inline score breakdown below the row (toggle). Also triggered by clicking the score circle. |
| 📋 (info) | Opens the full expansion card (same as clicking symbol) |
| ↗️ (matrix) | Jumps to Stock Matrix for this symbol |

Icons are subtle (gray, small) until hovered. On mobile, they're always visible.

---

## 16C. Display Style Configuration

### Score Display Styles
In `display.md` config or Settings, user can choose how scores are rendered:

| Style | Example |
|-------|---------|
| Circle (default) | Colored circle with number inside |
| Badge | Rounded rectangle with label (e.g., "72 Strong") |
| Plain | Just the number, colored text |
| Pill | Colored pill shape |

Applies to SA scores, MC scores, and tier badges independently.

### Number Formatting Options
Configurable in `display.md`:
- Decimal places for percentages (0, 1, or 2)
- Decimal places for prices (2 or 4 for penny stocks)
- Market cap format (abbreviated $492.7B vs full $492,700,000,000)
- Positive prefix: "+" or none
- Color for positive/negative/zero

### Color Token References in Config
When any config file needs to specify a color, it references a token name from `colors.md`:
```markdown
Chart VIX Line: Red-M
Chart MC Bars Up: Green-D
Chart MC Bars Down: Red-D
SA Score ≥70: Green-D
```
This ensures all colors are centrally managed. Change Green-D's hex in `colors.md` and it updates everywhere.

---

## 16D. Dynamic Normalization

### Problem
When questions are added/removed/reweighted, the raw score range changes. A score of 70 under the old system might correspond to 65 under the new one, breaking all the thresholds (tier rules, entry rules, color mappings).

### Solution: Anchor-Based Normalization

The normalization formula auto-adjusts when the config changes:

1. **Config declares min/max raw range:** When questions are added/modified, the system recalculates the theoretical min and max raw scores from the config.
2. **Normalization formula is always:** `((Raw - Min) / (Max - Min)) × 100`
3. **The min and max are auto-computed** from the config — NOT hardcoded. If you add a question worth 5 pts max and -2 min, the Max increases by 5 and Min decreases by 2 automatically.
4. **Result:** Normalized scores always fall 0-100 regardless of how many questions exist or how points change.

### Anchor Point Preservation
The system tracks an **anchor point** — a reference score that should maintain its meaning:
- Default SA anchor: 70 = "strong stock" threshold
- Default MC anchor: 60 = "overheated" threshold

When the config changes, the system shows:
```
⚠️ Config change detected.
Old raw range: -29 to +70 (span: 99)
New raw range: -29 to +75 (span: 104)
Old 70 normalized = 69.7 raw → New 70 normalized = 73.9 raw
Recommendation: Adjust tier thresholds or review entry rules.
```

This alert helps the user decide if thresholds need updating after config changes.

---

## 16E. Symbol Validation & ETF Handling

### Symbol Validation
When symbols are entered (typed, uploaded, or via watchlist):
- **Invalid symbol:** Red error toast: "❌ XYZ — Symbol not found"
- **Delisted:** Yellow warning: "⚠️ ABCD — Delisted (last traded: 2024-08-15)"
- **Valid but limited data:** Info badge showing what's available

### ETF Detection
When a symbol is an ETF (not an individual stock):
- **Alert column:** Shows "ETF" badge in the Alerts column
- **Scoring:** Run SA scoring with available data. Questions requiring fundamental data (revenue growth, margins, D/E, etc.) use missing data rules (midpoint assignment).
- **Tooltip on ETF badge:** "This is an ETF. Fundamental questions use estimated midpoints. Score may be less reliable."
- **Available for ETFs:** Price data, technical indicators, volume, momentum questions
- **Unavailable for ETFs:** Most fundamental questions (growth, margins, D/E, EPS, etc.)

---

## 16F. Navigation & State Persistence

### Back Button Behavior
Analysis results persist in memory during the session. When navigating:
- Run analysis on Analyzer → click into Score Matrix → press Back → Analyzer still shows results (no re-run needed)
- This applies to all page transitions within the app
- Implementation: Use React state management (context/Redux) to cache analysis results. Only clear on explicit "New Analysis" or page refresh.

### Calendar Date Persistence
All date pickers remember their last selected value:
- Score Matrix date picker → retains last selected backtest date
- MC Matrix date picker → retains last date range
- Analyzer date picker → retains last analysis date
- Stored in localStorage. Persists across sessions.

### URL State
Pages encode key state in the URL (e.g., `/score-matrix?date=2025-10-01&watchlist=love`). This allows:
- Bookmarking specific analysis views
- Sharing links
- Browser back/forward working correctly

---

## 16G. AI Summary Lines

### Stock Card One-Liner
In every stock's row (Analyzer table), between the symbol and the score, OR in the expansion card header:
- A single AI-generated sentence summarizing the stock
- Example: "Strong momentum play with accelerating growth, but P/E stretched at 47×"
- Generated during analysis alongside the full AI Assessment
- Shows as a muted-text subtitle under the company name

### Market Card One-Liner
In the MC bar (collapsed view):
- A single AI-generated sentence about market conditions
- Example: "Neutral market with bearish breadth divergence — selective buying only"
- Updates each time MC is scored

---

## 16H. AI-Powered Config Validation

When any config file is saved, the AI reviews it and checks for:

| Check | Example Error |
|-------|---------------|
| Point range mismatch | "Q5 max declared as 4 but highest threshold awards 5" |
| Missing thresholds | "Q10 has gap: no rule for D/E between 1.0 and 2.0" |
| Duplicate question IDs | "Two questions both named Q11" |
| Normalization mismatch | "Declared max raw 70 but actual sum of max points = 75" |
| Color token reference | "Score color rule references 'Teal-D' but that token doesn't exist in colors.md" |
| Logical inconsistency | "Entry rule says SA ≥ 55 for BUY but tier T2 starts at SA ≥ 50 — stocks could qualify for tier but not entry" |

Display errors as a validation panel below the editor. Each error is clickable → jumps to the relevant line.

---

## 16I. AI Trading Proactivity

### Proactive Ideas
The AI chat assistant should actively offer trading ideas when context warrants it:
- After scoring: "Based on these scores, your strongest candidates are MU (72), ANET (68), and PLTR (65). Given MC at 52, all three are in the BUY zone."
- When discussing optimization: "Your data shows Q11 (momentum magnitude) is your strongest predictor. Stocks with Q11=3 had 73% win rate — you might want to screen specifically for high momentum magnitude."
- Combine scoring data with Claude's own market knowledge: "NVDA scores 71 — your system rates it highly. I'd note that their next earnings on Feb 26 could be a catalyst, but also a risk event."

### AI as Stock Screener
Via chat commands:
- "Give me 20 hot stock ideas to test" → AI generates ticker list from its market knowledge → user can run scoring on them
- "What sectors are looking strong right now?" → AI provides sector analysis → suggests tickers
- "Find me high-momentum tech stocks" → AI suggests candidates matching criteria

---

## 16J. Chat — Image & File Input

### Image Upload
The chat box supports:
- **Paste screenshot** (Ctrl+V or mobile paste)
- **Upload image** (button in chat input bar)
- **Upload file** (CSV, PDF, Excel)

### Use Cases
- Paste a screenshot of your brokerage holdings → AI reads it, identifies tickers, runs scoring, provides analysis
- Upload a CSV of trades → AI analyzes performance vs scoring system
- Paste a chart screenshot → AI discusses technical patterns

### Implementation
- Images sent to Claude API as base64 via the vision capability
- Files parsed server-side (CSV → JSON, PDF → text) and included in context
- Chat input bar has: text input, 📎 (file upload), 📷 (image), 🎤 (voice — future)

---

## 16K. Optimization & Change Logging

### Optimization Run Log
Every optimization analysis is saved to an `optimization_log` table:

```
id | run_date | stock_count | stock_list_name | backtest_dates
config_version | results_summary (JSON) | notes | created_at
```

**Results summary includes:**
- Score bucket win rates (70+, 60-69, 50-59, <50)
- Top/bottom correlating questions
- Avg 1M return per bucket
- False positive/negative rates
- Any suggestions generated

### Display
- Accessible from Optimizer page → "Run History" tab
- Chronological log showing: date, stock count, key metrics, config version
- Compare any two runs side by side
- Prevents running in circles: "Last time you tested Q4 2024 data (Run #12, Jan 15), the 70+ bucket had 78% win rate with config v8"

### Config Change Log
Every config save creates an entry in `config_history`:
- Timestamp
- Which file changed
- Diff (what was added/removed/modified)
- Config version number

Display: Settings page → "Version History" tab → clickable timeline with diffs.

---

## 16L. Tooltips & Information Density

### Universal Tooltip Rule
**Every icon, colored circle, badge, abbreviated number, and signal indicator must have a tooltip.** No exceptions.

| Element | Tooltip Content |
|---------|----------------|
| SA Score circle | "SA Score: 72 (Strong) — Q11: 3, Q4: 5, Q5: 3..." (top contributing questions) |
| MC Score circle | "MC: 54 — Default: Teal-M, Contrarian: Blue-M" |
| MS signal icon | "BUY signal: MC 48 + SA 67 → Zone 5 rule (MC 40-49 + SA ≥55)" |
| Tier badge | "Tier 1: SA ≥55 ✓, Profitable ✓, MCap $152B ✓ → Max $100k" |
| Flag icons | "⚡ Volatility: 5D change +8.2% exceeded ±7% threshold" |
| AI indicator | "AI Assessment: 4 tags active (2 positive, 1 caution, 1 negative)" |
| Price color | "+5.7% (1 Month)" |
| Forward return | "+8.2% from $142.50 to $154.20 (22 trading days)" |

### Mobile Behavior
- On mobile, tooltips appear on tap (not hover)
- Tap once → tooltip appears
- Tap elsewhere → tooltip dismisses
- Long-press on any data cell → shows full tooltip + copy option

---

## 16M. Mobile Responsive Design

### Responsive Requirements
The app must be fully functional on mobile devices (phones and tablets).

### Mobile Layout Adaptations

| Desktop Element | Mobile Adaptation |
|----------------|-------------------|
| Side navigation tabs | Bottom tab bar (5 tabs max, "More" overflow) |
| Wide data tables | Horizontal scroll with sticky first column (Symbol) |
| Expansion cards | Full-width cards, stack vertically |
| Chart toolbar | Collapsible, essential toggles only |
| Chat panel | Full-screen overlay (swipe up from bottom) |
| Date picker | Native mobile date input |
| Settings editor | Simplified view (formatted only, edit mode in landscape) |

### Mobile-First Features
- **Quick Run:** Floating action button (FAB) → "Quick Analyze" → type/paste tickers → get scores immediately
- **Swipe gestures:** Swipe stock row left → quick actions (chart, matrix, remove)
- **Pull to refresh:** Refreshes current analysis
- **Push notifications:** (Future) Alert when a watchlist stock crosses a score threshold

### Breakpoints
- Desktop: ≥1024px (full layout)
- Tablet: 768-1023px (condensed sidebar, smaller charts)
- Mobile: <768px (bottom nav, stacked layout, horizontal scroll tables)

---

## 16N. AI Stock Discovery & Web Search

### Stock Discovery via Chat
The AI can suggest stock candidates through multiple methods:

| Method | Example Command |
|--------|----------------|
| AI knowledge | "Give me 20 momentum tech stocks" |
| Market screener | "Find stocks with >50% revenue growth and >$10B market cap" |
| Top movers | "Get today's top gainers over 5%" |
| Sector scan | "What semiconductor stocks are trending?" |
| Theme-based | "AI stocks that aren't NVDA or MSFT" |

### FMP Screener Endpoints
The app uses FMP's stock screener API for data-driven discovery:

| Endpoint | Use |
|----------|-----|
| `/stock-screener` | Filter by market cap, sector, price, volume, etc. |
| `/stock/gainers` | Today's top gainers |
| `/stock/losers` | Today's top losers |
| `/stock/actives` | Most active by volume |
| `/sector-performance` | Sector performance snapshot |

### Workflow
1. User asks AI for ideas → AI generates list (from knowledge + FMP screener)
2. AI auto-suggests: "Want me to run SA scoring on these 20 tickers?"
3. User confirms → analysis runs → results appear in chat AND Analyzer
4. User can save as watchlist directly from chat

---

## 16O. Signal Explanation Popups

### Every Signal is Explainable
When a user clicks or hovers on any trading signal, a popup explains the WHY:

**MS BUY Signal popup:**
```
🟢 BUY Signal
━━━━━━━━━━━━━━━
Rule: Zone 5 — MC 40-49 + SA ≥ 55
MC: 48
SA: 67 (Strong)
MS-A: 62.8
MS-C: 106.0
Tier: T1 (SA ≥55 ✓, Profitable ✓, MCap $152B ✓)
Max Position: $100,000 (VIX 17 → 100%)
```

**AVOID Signal popup:**
```
❌ AVOID
━━━━━━━━━━━━━━━
Veto V5: Momentum Divergence
52W Change: +62% (> 40% threshold)
3M Change: -8.4% (< -5% threshold)
Action: Do not enter. Stock showing distribution after extended run.
```

---

## 16P. FMP API Version & Data Integrity

### API Version Strategy
FMP has migrated from V3 to V4 and uses dynamic endpoint structures. The prompt should instruct the app:

1. **Check FMP documentation for the latest API version** at build time
2. **Use the newest available endpoints** — do not hardcode V3 paths
3. **Fallback:** If V4 endpoint fails, try V3 equivalent
4. **API base URL should be configurable** in General Settings (default: `https://financialmodelingprep.com/api/`)

### Data Integrity Rules — CRITICAL

**The app must NEVER estimate, fabricate, or interpolate missing data without explicit user notification.**

| Situation | App Behavior |
|-----------|-------------|
| API returns null for a field | Use missing data rules (midpoint). Show ⊙ icon with tooltip: "Data unavailable — midpoint estimate used" |
| Multiple fields missing (≥3 questions) | Show ⚠️ banner: "Warning: 5 questions used estimated values. Score reliability is reduced." |
| API call fails entirely | Show ❌: "Failed to fetch data for AAPL. Score not generated." Do NOT show a partial score without warning. |
| Stale cache data (>24h for live, acceptable for backtest) | Show 🕐 icon: "Data from cache (fetched 2 days ago)" |
| Symbol has very thin data (new IPO, foreign ADR) | Show "Limited Data" badge. List which questions were scoreable vs estimated. |

**Score Confidence Indicator:**
Each scored stock shows a confidence level:
- 🟢 High: ≤2 questions used missing data rules
- 🟡 Medium: 3-5 questions estimated
- 🔴 Low: 6+ questions estimated
- Displayed as small badge next to score. Tooltip shows which questions were estimated.

---

# 6. PAGE SPECIFICATIONS

## Navigation Structure

| Tab | Page |
|-----|------|
| Analyzer | Main dashboard (MC bar + stock table) |
| Lists | Watchlists + Analysis History + Matrix lists |
| Score Matrix | Multi-stock backtest by date |
| Stock Matrix | Single stock over time (SA Matrix in nav) |
| MC Matrix | Market over time |
| Optimizer | Correlation dashboard + suggestions |
| Settings | Config editor + rules display |

## Chat Panel
- Present on EVERY page as a collapsible bottom panel
- Default: Collapsed showing input bar + "Ask about stocks, trades, strategies..."
- Click to expand: Shows thread history + full chat
- Full-screen mode: Dedicated chat page with thread sidebar
- Thread list accessible from Lists page as well

## Lists Page Sections

| Section | Content |
|---------|---------|
| Watchlists | Named stock lists with create/edit/delete/reorder. Upload CSV to create. |
| Analysis History | Past analysis runs — each saved with name, date, stock list. Click to reload. |
| Stock Matrix | Tracked stocks with latest score + date range |
| Score Matrix | Saved matrix runs — named, dated, with summary stats (win rate, avg return) |
| Chat Threads | Saved AI conversations with page context tags |

### Saving Reports
Any analysis result can be saved as a named report:
- Click "Save" → enter name (or auto-generate from date + watchlist)
- Reports appear in Lists under the relevant section
- Reports preserve the exact config version used at time of creation
- Click any saved report to reload it in full

### CSV Download
Available on EVERY page with tabular data:
- Analyzer stock table
- Score Matrix
- Stock Matrix
- MC Matrix
- Optimizer correlation tables
- Export includes all visible columns + raw question points

## Settings Page Tabs

| Tab | Content |
|-----|---------|
| SA Scoring | sa-scoring.md config (formatted + edit views) |
| MC Scoring | mc-scoring.md config |
| Master Score | ms formulas, entry/exit rules, tiers |
| Flags | Flag definitions and triggers |
| Colors | Color tokens and mappings |
| Sectors | Sector classifications and lists |
| Display | Formatting, typography |
| Version History | Rollback to previous configs |

## Historical Mode Limitations
When running analysis on past dates:
- No AI Assessment content (quantitative only)
- No Fundamental Flags (news unavailable historically)
- AI Tags show no results (no data)
- Filing lag ~45 days from quarter end
- Chat can still analyze the quantitative data

---

# 7. DATA LAYER & FMP API

## FMP API Integration

Premium account. The app should NOT hardcode API endpoint URLs.

### Dynamic Endpoint Strategy

1. **API Base URL:** Configurable in General Settings (default: `https://financialmodelingprep.com/api/`)
2. **On first run or when prompted:** The app should check FMP's documentation at `https://site.financialmodelingprep.com/developer/docs` to identify the latest available endpoints and API version (V3, V4, or newer).
3. **Endpoint mapping is stored in a config file** (`api-endpoints.md`) — not hardcoded in the app. This allows the user or AI to update endpoint paths without code changes.
4. **Fallback chain:** If the configured endpoint fails, try the next known version (e.g., V4 → V3). Log the failure and alert the user.
5. **The AI chat can help troubleshoot:** "Endpoint X returned 404 — FMP may have moved this to V4. Want me to check?"

### Required Data Points (What We Need — Not How to Get It)

The scoring engine needs these data types. The `api-endpoints.md` config maps each to the current best FMP endpoint:

| Data Need | Used By |
|-----------|---------|
| Current/historical quote (price, change, volume) | SA, MC |
| Simple Moving Averages (50D, 200D) | SA, MC |
| RSI (14-day) | MC |
| MACD (12, 26, 9) + histogram | MC |
| Bollinger Bands (20, 2) | SA, MC |
| Income statement (quarterly + annual) | SA (revenue, OpIncome, net income, margins) |
| Cash flow statement (quarterly + annual) | SA (operating cash flow) |
| Balance sheet (quarterly) | SA (debt, equity) |
| Key metrics TTM | SA (ROE, ROA, P/E) |
| Historical daily prices | Backtesting (forward returns) |
| Institutional ownership | SA |
| Analyst ratings/consensus | SA, Flags |
| Insider trading activity | Flags |
| Earnings calendar | Flags |
| Treasury rates (10Y, 2Y) | MC (yield curve) |
| Stock news | AI Assessment, Flags |
| Stock screener / gainers / losers / actives | AI Stock Discovery |
| Sector performance | AI Stock Discovery |
| Company profile | Dashboard (sector, country, MCap, description) |

### Endpoint Config File (`api-endpoints.md`)

```markdown
## FMP Endpoint Mapping
Last verified: 2026-02-03
API Version: Check latest at https://site.financialmodelingprep.com/developer/docs

| Data Need | Endpoint Path | Notes |
|-----------|--------------|-------|
| Quote | /quote/{symbol} | Returns price, change, volume, MCap |
| Historical Prices | /historical-price-full/{symbol} | Add ?from=&to= for date range |
| SMA | /technical_indicator/daily/{symbol}?type=sma&period={n} | |
| RSI | /technical_indicator/daily/{symbol}?type=rsi&period=14 | |
| MACD | /technical_indicator/daily/{symbol}?type=macd | 12, 26, 9 default |
| Income Statement | /income-statement/{symbol}?period=quarter | |
| Cash Flow | /cash-flow-statement/{symbol}?period=quarter | |
| Balance Sheet | /balance-sheet-statement/{symbol}?period=quarter | |
| Key Metrics | /key-metrics-ttm/{symbol} | |
| Treasury Rates | /treasury?from={date}&to={date} | 10Y, 2Y |
| Company Profile | /profile/{symbol} | Sector, country, description |
...
```

**Important:** This config file is a starting point. The app should validate endpoints on first use and alert the user if any return errors or unexpected data formats. When FMP updates their API, the user (or AI) updates this config — no code changes needed.

### Historical Data for Backtesting

For backtesting, data must be fetched AS OF a specific historical date:
- Use `/historical-price-full/{symbol}?from=DATE&to=DATE` for point-in-time prices
- Technical indicators must be calculated from historical price series, not current values
- Financial statements have ~45 day filing lag from quarter end
- The system should use the most recent filing available as of the backtest date

### Caching Strategy
- Cache all API responses in database with timestamp
- Same-day requests → serve from cache
- Historical data is immutable → cache permanently
- Forward prices for backtesting → fetch once, store forever
- MC data (multiple tickers) → batch where possible

### Cache & Data Refresh Controls

**Per-Stock Refresh:** Every scored stock has a 🔄 refresh icon. Clicking it:
- Clears cached API data for that symbol
- Re-fetches fresh data from FMP
- Re-runs the scoring engine
- Updates the stored score in the database

**Matrix Refresh & Rerun:**

All three matrices (Score, Stock, MC) support:

| Action | What It Does |
|--------|-------------|
| **Refresh row** | 🔄 on individual row — re-fetches data and re-scores that entry |
| **Rerun selection** | Checkboxes → "Rerun Selected" — re-scores selected entries with current config |
| **Rerun time period** | "Rerun Period" → date range picker → re-scores ALL entries in that range with current config + fresh API data |
| **Delete selection** | Select rows → "Delete" — removes entries from database |
| **Delete time period** | "Delete Period" → date range → removes all entries in range |
| **Rerun all** | "Rerun All" — re-scores entire matrix with current config |

**Use cases:**
- Config changed → rerun a time period to see new scores (before/after display applies)
- Suspected bad API data → refresh specific dates to re-fetch
- Clean up test runs → delete a time period
- New question added → rerun historical data to populate the new column

**When re-running with a new config:** The system stores new results with the new config version. Toggle "Keep original scores" checkbox to preserve originals for comparison, or overwrite them.

**Bulk Operations Warning:** Large re-runs trigger many API calls. The app shows an estimate ("This will make ~450 API calls. Continue?") before executing.

---

# 8. CONFIGURATION SYSTEM

All scoring rules, thresholds, colors, and display settings are stored in editable markdown-format config files. The app parses these configs at runtime.

## General Settings Panel

A dedicated settings panel for app-wide preferences (separate from scoring configs):

| Setting | Default | Description |
|---------|---------|-------------|
| FMP API Key | (user enters) | Financial Modeling Prep API key |
| Claude API Key | (user enters) | Anthropic API key for chat |
| Account Type | Active | Investment / Active / IRA — affects AI recommendations |
| Cash Target | $140,000 | Displayed in MC row, used in AI chat suggestions |
| Default Watchlist | (none) | Auto-loads on app open |
| Default Hold Period | 1M (22 days) | Primary forward return window for backtesting |
| Price Period Columns | Day, 5D, 1M, 3M, 1Y | User can toggle which time periods show in tables. Options: 1D, 5D, 10D, 2W, 1M, 6W, 2M, 3M, 6M, 1Y, 2Y |
| Forward Return Windows | 5D, 1M, 3M | Configurable backtest forward periods. User can change to any combo (e.g., 2W, 6W, 2M). Specified in trading days. |
| Chart Default Period | 1Y | Default time range on chart load |
| Theme | Dark | Dark / Light (dark is primary) |

API keys are stored securely (environment variables or encrypted in DB — never in config files).

## Config Architecture

```
/configs
├── sa-scoring.md        ← SA questions, points, thresholds
├── mc-scoring.md        ← MC questions, points, thresholds, modifiers
├── master-score.md      ← MS formulas, entry/exit rules, tiers, vetoes
├── flags.md             ← Flag definitions and triggers
├── colors.md            ← Color tokens and score-to-color mappings
├── sectors.md           ← Sector classifications and stock lists
└── display.md           ← Number formatting, typography, layout rules
```

## Settings Page UI

### Config Editor
- Left sidebar: File list (sa-scoring.md, mc-scoring.md, etc.)
- Main area: Markdown editor with:
  - **Formatted view** (default): Rendered markdown, read-friendly
  - **Edit view** (toggle): Raw markdown editor with syntax highlighting
- **Save** button: Validates config, saves to DB, triggers re-parse
- **Reset** button: Reverts to last saved version
- **Version history:** List of saved versions with timestamps, ability to rollback

### Validation
On save, the engine validates:
- All questions have required fields (id, max, min, fields)
- Point values are within declared max/min range
- No duplicate question IDs
- Normalization formula matches declared raw range
- Color tokens reference valid token names
- Sector lists contain valid ticker symbols

### How the Engine Uses Configs

1. On app startup (or config save), parse all .md config files
2. Convert to internal JSON representation in memory
3. Scoring engine reads the JSON to calculate scores
4. Config changes take effect immediately on next analysis run
5. Historical scores are NOT retroactively recalculated (they preserve the config version used)

### Two-Way Config Sync

When the optimizer suggests weight changes and the user accepts:
1. The app auto-updates the relevant config file (e.g., sa-scoring.md)
2. Shows a diff view: old values → new values (highlighted in green/red)
3. User confirms the change before it's committed
4. Config version increments automatically
5. The formatted view in Settings reflects the change immediately

This ensures the config file the user sees is always the source of truth — whether changes come from manual editing or optimizer acceptance.

### Config Versioning
- Each saved config gets a version number + timestamp
- Score records store the config version used
- This allows comparing "how would old scores look under new config" in the optimizer

---

# 9. SCORING ENGINE (Layer 1: Scores)

The scoring engine reads question definitions from editable markdown config files, fetches required data from FMP API, and calculates scores. Penalties and vetoes are integral parts of the score calculation — penalties deduct points from the total SA, MC, or MS score, while vetoes override or cap the final result based on risk conditions. Both are defined in the config files alongside questions.

**Critical Design Principle:** The engine is CONFIG-DRIVEN. All question definitions, point values, thresholds, weights, penalties, and vetoes live in editable config files — NOT hardcoded. The engine reads the config and applies logic generically. The user can add/remove/modify questions, change point values, and adjust thresholds via the Settings page without touching code. Question counts are dynamic — the engine processes however many questions the config defines.

**Test Before Deploy:** Config changes are NOT applied immediately. When the user modifies a config file:
1. **Save as Draft** — changes are saved but not active
2. **Test** — re-runs the most recent analysis (or a selected backtest) using the draft config. Results appear side-by-side with the current live config results.
3. **Before/After Display:** The pre-modification scores appear in smaller, faded text next to the new scores. Example: `72 ₍₆₈₎` — new score 72, old score 68 shown smaller and muted. This applies to total scores, individual question points, tier assignments, and signals.
4. **Deploy** — clicking the Deploy button commits the draft config as the new active version. Config version increments. All future analyses use the new config.
5. **Discard** — reverts to the current active config, draft is deleted.

**Single-Source Principle:** Question counts, max/min raw ranges, and normalization bounds are NEVER hardcoded anywhere in the app or this document. The engine dynamically counts questions, computes the total possible max and min scores, and derives the normalization formula from the config at runtime. Adding or removing a question in the config file should require NO changes anywhere else — no updating aggregates, no editing normalization formulas, no modifying other files. The config is the single source of truth.

**Self-Diagnostic & Data Integrity System:** The scoring engine continuously validates its own inputs, calculations, and outputs. It alerts the user to any issues and suggests fixes when possible.

| Check Type | What It Catches | Example |
|------------|----------------|---------|
| **Data Format Validation** | API returns wrong format (raw dollars instead of %, decimal vs whole number, string vs number) | "⚠️ Q4 Net Margin: FMP returned 0.15 — expected percentage. Interpreting as 15%. Verify this is correct." |
| **Range Sanity Check** | Values outside expected bounds (revenue growth of 50,000%, negative volume, P/E of -0.003) | "⚠️ AAPL Q1 Sales Growth: 52,847% — abnormally high. Likely raw dollar value instead of %. Skipping, midpoint assigned." |
| **Endpoint Validation** | API endpoint returns data that doesn't match what the question expects | "⚠️ Q12 EPS Growth: Endpoint returned TTM EPS ($4.52) instead of EPS growth %. Check endpoint mapping in config." |
| **Calculation Contradictions** | Scoring results that contradict each other logically | "⚠️ TSLA: Q4 (Net Margin) = 5 pts (≥30%) but Q17 (Cash Burn) triggered (-3 pts, unprofitable). Contradiction — verify margin data." |
| **Config Redundancy** | Two questions scoring essentially the same data point | "ℹ️ Q19 (Trend Consistency) and Q11 (Momentum Magnitude) both use 52W and 3M price changes. Consider if both are needed." |
| **Stale/Missing Endpoints** | API endpoint returns 404, empty data, or hasn't updated | "❌ FMP endpoint /key-metrics-ttm/XYZ returned 404. Question Q13 (ROE) cannot be scored. Check if endpoint URL has changed." |
| **Normalization Drift** | After config changes, normalized scores shift significantly from historical norms | "⚠️ New config produces avg normalized SA of 58 vs historical avg of 52. Threshold review recommended." |
| **Cross-Question Consistency** | Related questions producing conflicting signals | "ℹ️ MU: Q5 (1M Price +18%) scores 3 but Q21 (Momentum Divergence) triggers -10. Both are working correctly, but flagging for review." |

**Alert Levels:**
- ❌ **Error:** Scoring halted or unreliable for this question/stock. Requires user attention.
- ⚠️ **Warning:** Score calculated but may be incorrect. User should verify.
- ℹ️ **Info:** No error, but something worth reviewing (redundancy, unusual pattern).

**Alert Display:**
- Alerts appear in a notification panel (bell icon in top bar) with count badge
- Each alert links to the specific stock + question that triggered it
- Alerts are also visible in the AI chat context — the AI can explain and suggest fixes
- Persistent alerts (config issues) stay until resolved. Transient alerts (data issues) clear on next run.

**Auto-Suggest Fixes:**
When possible, the system suggests a specific fix:
- "FMP V3 endpoint deprecated → Try V4 equivalent: `/v4/key-metrics/...`"
- "Revenue returned as raw number (1,500,000,000) → Add conversion: divide by previous period for growth %"
- "Q6 and Q23 both use Bollinger Bands — consider merging into one question"

---

## 3A. Market Condition Score (MC)

Scores the overall market environment using QQQ as proxy. Run before stock recommendations.

### Required Tickers & Data Points

| Ticker | Data Points |
|--------|-------------|
| QQQ | Price, 50D SMA, 200D SMA, RSI (14), 52-Week %Chg, Bollinger Bands (20, 2), MACD (12, 26, 9), 52W High, 52W Low |
| SPY | Price, 50D SMA, 1-Month %Chg, 3-Month %Chg |
| ^VIX | Price (Level), 3-Month %Chg, 1-Month %Chg |
| IWM, RSP, IYT, EFA, XLY, XLP, XLE, GLD, HYG, XLI, UUP, TLT, IEF | 1-Month %Chg each |

### Derived Calculations

1. **MACD Direction:** Compare Current Histogram vs Previous Day. Strengthening = Current > Previous. Weakening = Current < Previous.
2. **Bollinger %B:** `(Price - Lower Band) / (Upper Band - Lower Band)`
3. **Yield Curve Proxy:** Fetch 10Y and 2Y Treasury Rates. Alternative: (TLT 1M %Chg) minus (IEF 1M %Chg).

### Missing Data Handling

If data unavailable: Calculate midpoint of (Max + Min) ÷ 2, round toward zero. Q6 (skipped): always 0.

### MC Questions — Technical

**Q1: QQQ Price vs Moving Averages** — Max: +5 / Min: -5
- Fields: QQQ Price, 50D SMA, 200D SMA
- +5: Above both 50MA and 200MA
- +2: Above 200MA only (below 50MA)
- 0: At/near both MAs (± 1%)
- -2: Below 200MA only (above 50MA)
- -5: Below both 50MA and 200MA

**Q2: Trend Confirmation (50MA-200MA Spread)** — Max: +5 / Min: -5
- Fields: QQQ 50D SMA, QQQ 200D SMA
- +5: 50MA ≥ 5% above 200MA
- +3: 50MA above 200MA, < 5% spread
- 0: MAs flat or converging (± 1%)
- -3: 50MA below 200MA, < 5% spread
- -5: 50MA ≥ 5% below 200MA

**Q3: RSI Momentum** — Max: +5 / Min: -3
- Field: QQQ RSI (14)
- +5: RSI < 30 (oversold)
- +3: RSI 50-60
- +2: RSI 40-50
- +1: RSI 60-70 OR RSI 30-40
- -3: RSI > 70 (overbought)

**Q4: VIX vs 3-Month Average** — Max: +5 / Min: -5
- Field: VIX 3-Month %Chg
- +5: < -20% | +3: -20% to -10% | 0: -10% to +10% | -3: +10% to +20% | -5: > +20%

**Q5: VIX Absolute Level** — Max: +3 / Min: -5
- Field: VIX Price
- +3: 14-18 | +2: < 14 | +1: 18-22 | -1: 22-25 | -3: 25-30 | -5: > 30

**Q6: Intermarket Correlation** — SKIP. Always 0.

**Q7: QQQ 52-Week Strength** — Max: +5 / Min: -5
- Field: QQQ 52W %Chg
- +5: ≥ 20% | +3: 10-20% | +1: 0-10% | -1: -10% to 0% | -3: -20% to -10% | -5: < -20%

**Q8: MACD Score** — Max: +4 / Min: -4
- Fields: MACD Histogram (12, 26, 9), Previous Day Histogram
- +4: Buy + Strengthening | +2: Buy + Weakening | +1: Buy + Weakest (Near Zero)
- -1: Sell + Weakest | -2: Sell + Weakening | -4: Sell + Strengthening

**Q9: QQQ Range Position** — Max: +3 / Min: -2
- Formula: `(Price - 52W Low) / (52W High - 52W Low)`
- +3: < 0.10 | +1: 0.10-0.45 | 0: 0.45-0.55 | +1: 0.55-0.90 | -2: > 0.90

### MC Questions — Macro

**Q10: GDP Growth Proxy (SPY 3M %Chg)** — Max: +3 / Min: -3
- +3: > 10% | +2: 6-10% | 0: 3-6% | -2: 0-3% | -3: Negative

**Q11: Federal Reserve Policy (TLT-IEF Spread)** — Max: +3 / Min: -3
- Calculate: (TLT 1M %Chg) minus (IEF 1M %Chg)
- +3: > 2% | +2: 0-2% | 0: -1% to 0% | -1: -2% to -1% | -3: < -2%

**Q12: Unemployment Trend (XLI)** — Max: +2 / Min: -2
- +2: > 3% | +1: 0-3% | 0: -2% to 0% | -1: -5% to -2% | -2: < -5%

**Q13: Treasury Yield Curve** — Max: +3 / Min: -3
- Calculate: (10Y Rate - 2Y Rate)
- +3: > 1% | +1: 0-1% | 0: -0.5% to 0% | -2: -1% to -0.5% | -3: < -1%

### MC Questions — Breadth & Intermarket

**Q14: International (EFA vs SPY)** — Max: +2 / Min: -2
- +2: EFA beats > 3% | +1: 1-3% | 0: ± 1% | -1: Lags 1-3% | -2: Lags > 3%

**Q15: Commodities (XLE + GLD)** — Max: +2 / Min: -2
- "Up" = > +3%, "Down" = < -3%, "Stable" = between
- +2: XLE Up + GLD Down | +1: XLE Up + GLD Stable | 0: Mixed | -1: XLE Down + GLD Stable | -2: XLE Down + GLD Up

**Q16: Small-Cap Breadth (IWM vs QQQ)** — Max: +3 / Min: -3
- +3: IWM beats > 3% | +1: 1-3% | 0: ± 1% | -1: Lags 1-3% | -3: Lags > 3%

**Q17: Sector Rotation (XLY vs XLP)** — Max: +2 / Min: -2
- +2: XLY beats > 3% | +1: 1-3% | 0: ± 1% | -1: Lags 1-3% | -2: Lags > 3%

**Q18: Equal-Weight (RSP vs SPY)** — Max: +2 / Min: -2
- +2: RSP beats > 2% | +1: 0.5-2% | 0: ± 0.5% | -1: Lags 0.5-2% | -2: Lags > 2%

**Q19: Transportation (IYT vs SPY)** — Max: +2 / Min: -2
- +2: IYT beats > 2% | +1: 0.5-2% | 0: ± 0.5% | -1: Lags 0.5-2% | -2: Lags > 2%

### MC Questions — Credit & Sentiment

**Q20: Credit Spreads (HYG)** — Max: +2 / Min: -2
- +2: > 1% | +1: 0-1% | 0: -0.5% to 0% | -1: -1% to -0.5% | -2: < -1%

**Q21: Dollar Index (UUP)** — Max: +2 / Min: -2
- +2: < -2% | +1: -2% to 0% | 0: 0-1% | -1: 1-2% | -2: > +2%

**Q22: Volatility Trend (VIX 1M %Chg)** — Max: +2 / Min: -2
- +2: < -10% | +1: -5% to -10% | 0: ± 5% | -1: +5% to +10% | -2: > +10%

### MC Modifiers (Apply After Base Score)

| ID | Modifier | Points | Trigger |
|----|----------|--------|---------|
| M1 | Euphoria Penalty | -5 | QQQ RSI > 75 |
| M2 | Breadth Divergence | +(Breadth Section Score) | QQQ > 50SMA BUT Breadth Section < 0 |
| M3 | VIX Divergence | -5 | QQQ 1D Up > 1% AND VIX 1D Up > 5% |
| M4 | Yield Curve Risk | -3 | Yield Spread < -0.8% AND RSI > 70 |
| M5 | Momentum Fatigue | -3 | MACD Histogram < 0 AND QQQ > 50SMA |

### MC Normalization — Dynamic

The normalization formula is auto-calculated from the config. The engine:
1. Sums all MC question max points + modifier max points → **Max Raw**
2. Sums all MC question min points + modifier min points → **Min Raw**
3. Applies: `((Raw Score - Min) / (Max - Min)) × 100`

These values update automatically whenever the MC config is saved/deployed. Never hardcode them.

*Reference values (current config):* Raw range approximately -93 to +65, span ~158. These will change if questions are added/removed.

---

## 3B. Stock Analysis Score (SA)

Score each stock individually. All data from FMP API.

### Missing Data Handling
- Positive-only range (e.g., 0-6): assign middle value (e.g., 3)
- Negative-only range: assign 0
- Both positive/negative (e.g., -5 to +5): assign 0
- Penalty questions (max 0): assign 0

**Missing Data Alerts:** Every question that uses a missing data fallback must:
- Show ⊙ icon in the score breakdown card for that question
- Tooltip on icon: "Data unavailable — midpoint estimate used (3 of 6)"
- Appear in the notification panel as ⚠️ warning
- The stock's overall score shows a confidence indicator based on how many questions used fallback values (see 16P)

### Manual Score Adjustments

Users can apply manual point adjustments to any stock or symbol, either permanently or for specific dates. Defined in a `manual-adjustments.md` config file.

**Config Format:**
```markdown
## Permanent Adjustments
| Symbol(s) | Adjustment | Reason |
|-----------|-----------|--------|
| MSTR | -3 | CEO risk — until leadership change |
| COIN | -2 | Regulatory overhang |
| PAAS, SLV, WPM, AG, SILV, MAG | -2 | Silver sector — manual group penalty |
| IREN, MARA, CLSK, RIOT | -4 | Crypto mining — additional risk |

## Date-Specific Adjustments
| Symbol(s) | Date | Adjustment | Reason |
|-----------|------|-----------|--------|
| AAPL | 2025-10-15 | +2 | Post-event clarity, temporary boost |
| TSLA, RIVN, LCID | 2025-12-01 | -5 | Pre-earnings uncertainty — EV sector |
```

**Rules:**
- **Multiple symbols:** Comma-separated symbols apply the same adjustment to all listed tickers. Useful for sector/theme groups that don't map to a standard sector classification.
- Adjustments are applied AFTER the raw score is calculated but BEFORE normalization
- They appear as a separate line in the score breakdown: "Manual Adjustment: -3 (CEO risk)"
- Shown with a 🔧 icon and distinct color (Purple-M) so they're clearly visible and not confused with scored questions
- Date-specific adjustments only apply on that exact date. Permanent adjustments apply on all dates until removed.
- Adjustments can be added/edited/removed via Settings page or directly from the stock expansion card (quick-add button)
- The AI chat is aware of active adjustments and factors them into trading discussions

### Growth Questions — All Use Same Scale

| Pts | Condition |
|-----|-----------|
| 6 | Annual ≥50% AND Quarterly > Annual |
| 5 | Annual ≥50% AND Quarterly > 0 but ≤ Annual |
| 5 | Annual 20-49% AND Quarterly > Annual |
| 4 | Annual 20-49% AND Quarterly > 0 but ≤ Annual |
| 2 | Annual 1-19% AND Quarterly > 0 |
| 1 | Annual ≥0 AND Quarterly ≤ 0 |
| 1 | Annual < 0 AND Quarterly > 0 |
| 0 | Annual < 0 AND Quarterly ≤ 0 |

**Q1: Sales Growth** — Annual/Quarterly Revenue Growth % (YoY)
**Q2: Operating Income Growth** — Annual/Quarterly OpIncome Growth % (YoY)
**Q3: Cash Flow Growth** — Annual/Quarterly OpCashFlow Growth % (YoY)

**Cyclical Sector Cap:** Basic Materials, Energy, Mining — max 4 pts each Q1-Q3. Exception: If Q23 = Breakout (4 pts), cap lifts to 6.

### Remaining SA Questions

**Q4: Net Profit Margin** — Max: 5 / Min: 0
- 5: ≥30% | 4: >20% | 3: >15% | 2: >10% | 1: >5% | 0: ≤5%

**Q5: 1-Month Price Change** — Max: 4 / Min: 0
- ≥20%: 4 | >10%: 3 | >5%: 2 | >0%: 1 | ≤0%: 0

**Q6: 10-Day Price Change** — Max: 3 / Min: 0
- ≥15%: 3 | >10%: 2 | >5%: 1 | ≤5%: 0

**Q7: 20-Day Average Volume** — Max: 3 / Min: 0
- >1M: 3 | >500K: 2 | >200K: 1 | ≤200K: 0

**Q8: Ownership & Coverage** — Max: 2 / Min: 0
- Institutional Ownership > 0%: +1 | Analyst Ratings > 0: +1

**Q9: Price vs Moving Averages** — Max: 4 / Min: 0
- Above both 50D & 200D: 4 | Above 50D only: 3 | Above 200D only: 2 | Below both: 0

**Q10: Debt-to-Equity** — Max: 3 / Min: -3 (evaluate in order, stop at first match)
- Negative equity: -3 | <0.5: 3 | <1.0: 2 | <2.0: 1 | ≥2.0: 0

**Q11: Momentum Magnitude** — Max: 3 / Min: 0
- Formula: 52W %Chg + 3M %Chg
- >150%: 3 | >100%: 2 | >50%: 1 | ≤50%: 0

**Q12: EPS Growth Prior Year** — Max: 4 / Min: 0
- ≥100%: 4 | >50%: 3 | >25%: 2 | >0%: 1 | ≤0%: 0

**Q13: Return on Equity** — Max: 2 / Min: 0
- >20%: 2 | >10%: 1 | ≤10%: 0

**Q14: Return on Assets** — Max: 3 / Min: 0
- ≥15%: 3 | >10%: 2 | >5%: 1 | ≤5%: 0

**Q15: Options Available** — Max: 1 / Min: 0 — Yes: 1 | No: 0

**Q16: Country** — Max: 1 / Min: 0 — USA: 1 | Other: 0

**Q17: Cash Burn Risk** — Max: 0 / Min: -3
- 0: Profitable OR Unprofitable but OpCF ≥ 0 OR MCap ≥ $10B
- -3: Unprofitable + Neg OpCF + MCap < $10B

**Q18: Deterioration Risk** — Max: 0 / Min: -3
- Trigger: Q Revenue < q-4 AND Q OpIncome < q-4 AND Q OpCF < q-4
- Combined Cap: If both Q17+Q18 trigger, total = -5 (not -6)

**Q19: Trend Consistency** — Max: 3 / Min: 0
- Both 3M and 52W positive: 3 | One positive: 1 | Both negative: 0

**Q20: Financial Strength** — Max: 3 / Min: 0
- Margin ≥20% + ROE ≥20% + D/E <0.5: 3
- Margin ≥10% + ROE ≥10% + D/E <1.5: 2
- Margin ≥0% + ROE ≥5% + D/E <3.0: 1
- Else: 0

**Q21: Momentum Divergence Penalty** — Max: 0 / Min: -10
- Trigger: 52W > 40% AND 3M < -5% → -10

**Q22: Sector Preference** — Max: 2 / Min: -4
- +2: Tech, Finance, Business Services, Aerospace, Defence
- +1: Consumer, Auto, Retail, Medical, Industrial, Transport, Utilities, Construction
- 0: Energy, Materials, Gold/Mining, Pharma
- -4: Crypto (IREN, MARA, CLSK, RIOT, BITF, WULF, HUT, CIFR, COIN, MSTR, CORZ, BTBT, HIVE, BTDR)
- Gold/Mining: NEM, GFI, KGC, AU, HMY, CDE, HL, NGD, CGAU, AEM, GOLD, AG

**Q23: %B Position (Bollinger Bands)** — Max: 4 / Min: 0
- Breakout (>100%): 4 | Buy zone (≤20%): 3 | Mid (20-80%): 2 | Upper (80-95%): 1 | Near top (>95%): 0

**Q24: Momentum Quality** — Max: 2 / Min: -1 (evaluate in order, stop at first match)
- Spike Risk: 10D > 20% AND 1M < 10% → -1
- Smooth: 52W > 0 AND 3M > 0 AND 1M > 0 AND 52W > 3M > 1M > 10D → +2
- Accelerating: 3M > 0 AND (3M × 4) > 52W → +1
- Other: 0

**Q25: High P/E Momentum Trap** — Max: 0 / Min: -5
- Trigger: P/E > 50 AND 10D < -5% → -5

**Q26: Biotech Binary Event Burn** — Volatility Cap
- Applies to: Healthcare (Biotech, Medical Devices, Drug Manufacturers)
- Trigger: 10D > 15% → Override normalized score to max 55

**Q27: Sustained Downtrend** — Max: 0 / Min: -3
- Check 1D, 5D, and 1M price changes
- ALL THREE negative: **-3**
- Otherwise: **0**
- Rationale: No positive momentum at any timeframe. Wait for reversal before entering.

**Q28: Sudden Drop / Slide** — Max: 0 / Min: -10
- Evaluate each of the last 3 trading days individually (close-to-close). Also check cumulative 5-day return. Highest matching tier only (no stacking):

| Tier | Condition | Penalty |
|------|-----------|---------|
| Critical | Any single day ≥ 15% drop within last 3 days | -10 |
| Severe | Any single day ≥ 10% drop within last 3 days | -5 |
| Caution | Any single day ≥ 7% drop within last 3 days, OR cumulative 5-day return ≤ -10% | -3 |
| None | No conditions met | 0 |

- Self-Healing: Clears once the drop day falls outside the 3-day window. If decline continues, the 5-day cumulative condition sustains the Caution tier.
- Rationale: Tiered response to abnormal drops. 15%+ effectively makes the stock untradeable by score alone.

**Q29: Short Interest Risk** — Max: 0 / Min: -2
- High short interest amplifies volatility in both directions.
  | Short % Float | Penalty |
  |---------------|---------|
  | > 30% | -2 |
  | > 20% | -1 |
  | ≤ 20% | 0 |
- Data Source: FMP short interest endpoint
- Rationale: Heavily shorted stocks have unpredictable, amplified moves. For 1-month swing trades, this adds risk regardless of direction.

**Q30: Low Float Risk** — Max: 0 / Min: -1
- Low float stocks have amplified price swings on normal volume.
  | Float | Penalty |
  |-------|---------|
  | < 20M shares | -1 |
  | ≥ 20M shares | 0 |
- Data Source: FMP company profile (float shares)

**Q31: Sector Performance vs Peers** — Max: +1 / Min: -1
- Compare stock's 1M return vs its sector average 1M return.
  | Condition | Points |
  |-----------|--------|
  | Outperforms sector by > 10% | +1 |
  | Within ±10% of sector | 0 |
  | Underperforms sector by > 10% | -1 |
- Data Source: FMP sector performance + stock quote
- Rationale: Relative strength vs peers indicates stock-specific quality beyond market/sector movement.

### SA Normalization — Dynamic

Same as MC: auto-calculated from the config on every save/deploy.
1. Sums all SA question max points → **Max Raw**
2. Sums all SA question min points (including penalties) → **Min Raw**
3. Applies: `((Raw Score - Min) / (Max - Min)) × 100`

*Reference values (current config):* Raw range approximately -42 to +71, span ~113. These will change if questions are added/removed.

---

## 3C. Master Score (MS) — Configurable Formulas (Layer 1 Scores + Layer 3 Decisions)

The MS combines SA and MC into trading signals. **Formulas are defined in the `master-score.md` config file** and can be modified, tested, and optimized like any other config.

### Formula Definitions (in config)

```markdown
## Formulas

### Formula A (User)
Expression: (SA + (3 * MC)) / 4
Label: MS
Description: Weighted average favoring market conditions

### Formula B (Contrarian)
Expression: SA + 0.75 * (100 - MC)
Label: C
Description: Rewards buying quality during fear
```

The user can:
- **Edit formulas** in the config using SA and MC as variables, plus constants
- **Add new formulas** — the engine parses any valid math expression using SA and MC
- **Add penalties/bonuses** to formulas: e.g., `(SA + 3*MC) / 4 - 5` when VIX > 30
- **Test formulas** using the Draft → Test → Deploy workflow. Backtest shows all formula results side by side.
- **Select which formula to optimize against** in the Optimization Engine — dropdown to pick "MS-A", "MS-C", or any custom formula

### Formula Testing in Optimization
The optimizer can run correlation analysis against any selected formula:
- "Which formula has the best correlation with 1M forward returns?"
- Side-by-side comparison: Formula A win rate vs Formula B win rate vs any custom
- The AI chat can discuss formula performance and suggest modifications

### Conditional Modifiers (in config)
Formulas can include conditional bonuses/penalties:
```markdown
## MS Modifiers
| Condition | Modifier | Applies To |
|-----------|----------|------------|
| VIX > 30 | -5 | All formulas |
| SA Q23 = Breakout AND MC < 60 | -3 | All formulas |
| SA > 70 AND MC < 30 | +10 | Formula B only |
| MC > 75 | -3 | All formulas |
```

### Portfolio Concentration Limits
Enforced at the decision layer — does not affect scores, but the dashboard warns when limits are reached.
```markdown
## Portfolio Rules
| Sector Group | Max Positions |
|-------------|---------------|
| Pharma/Medical | 2 |
| Crypto | 2 |
| Mining/Commodities | 2 |
| Oil/Gas/Energy | 2 |
```
When a sector limit is reached, additional stocks in that sector show "⚠️ Sector limit reached" in the Alerts column regardless of score.

All active formulas run simultaneously for comparison and optimization.

### Formula A (User) — Displayed first
```
MS-A = (SA + (3 × MC)) / 4
```
Range: 0-100. Weighted average favoring market conditions.

### Formula B (Contrarian) — Displayed as "C:"
```
MS-C = SA + 0.75 × (100 - MC)
```
Range: 0-175. Rewards buying quality stocks during fear.

### Display Format (everywhere MS appears)
```
MS: 68
C: 117.5
```

### Entry Rules (Zone Table)

| Priority | Condition | Signal |
|----------|-----------|--------|
| 1 | VIX ≥ 28 | 🚨 VIX OVERRIDE — Buy QQQ, 50% T1 |
| 2 | MC < 20 | 🛑 PANIC — All cash |
| 3 | MC 20-29 + SA ≥ 55 | 🟢 BUY |
| 4 | MC 30-39 + SA ≥ 55 | 🟢 BUY |
| 5 | MC 40-49 + SA ≥ 55 | 🟢 BUY |
| 6 | MC 50-59 + SA ≥ 65 | 🟡 SELECTIVE BUY |
| 7 | MC 60-74 | ⛔ NO NEW BUYS |
| 8 | MC ≥ 75 | ⛔ NO NEW BUYS |

### Exit Rules

| Priority | Condition | Action |
|----------|-----------|--------|
| 1 | VIX < 18 + QQQ profit > 10% | Exit VIX Override |
| 2 | Down -20% from entry | Emergency stop |
| 3 | SA < 45 + down > 15% | Sell |
| 4 | +20% T1 / +25% T2 / +30% T3 | Sell 50% |
| 5 | 60 days + SA < 55 | Sell |
| 6 | Under 20 days held | Don't exit |

### Tiers

| Tier | Criteria | Max Position |
|------|----------|-------------|
| T1 | SA ≥ 55 + Profitable + MCap > $50B | $100,000 |
| T2 | SA ≥ 50 + Profitable + MCap > $2B | $50,000 |
| T3 | SA ≥ 50 + MCap > $100M + (growth OR small profitable) | $20,000 |
| SELL | None qualify | $0 |

### VIX Position Adjustment
- VIX < 18: 100% | 18-25: 75% | 25-35: 50% | ≥ 35: No new buys (except Override)

### Vetoes → AVOID

**SA:** V1: Biotech+10D>15% → Cap 55 | V2: P/E>50+10D<-5% | V3: Unprofitable+NegOCF+MCap<$10B | V4: Rev+OpInc+OCF all declining QoQ | V5: 52W>40%+3M<-5%
**MC:** V1: VIX>35 → Cap MC 30 | V2: QQQ 52W<-30% → Cap MC 20 | V3: QQQ below both MAs+VIX>30 → PANIC

---

# 10. TAGS & ALERTS (Layer 2: Assessment)

Non-scoring visual indicators providing context beyond SA/MC scores. Part of the Assessment layer — informs human judgment without modifying scores. See Three-Layer Framework in Section 1.

## Kill Switches (Immediate Action)
| Flag | Icon | Trigger |
|------|------|---------|
| SEC/Fraud | 🚨 | Active investigation, fraud, restatement |

## Risk Tags
| Tag Text | Trigger |
|----------|---------|
| Earnings Soon | Earnings within 14 days |

> **Note:** Downtrend, Volatility, Critical Volatility, Short Squeeze, Low Float, and Sector Lag have been moved into SA as scored questions (Q27-Q31). They are data-driven and reproducible, so they belong in the scoring layer for backtesting and optimization. Sector flags removed — handled by Q22/Q26 and MS Portfolio Rules. Mean Reversion moved to MS modifiers.

## Opportunity Tags
| Tag Text | Trigger |
|----------|---------|
| Bounce Play | SA < 35 AND 1M < -15% |
| Momentum Spec | SA < 35 AND 1M > +15% |
| Turtle | 52W ±10% AND 1M ±10% |
| Lottery | SA < 35 AND MCap < $500M |
| IPO | Listed < 12 months |

## Fundamental Tags
| Tag Text | Trigger |
|----------|---------|
| Guidance ↑ | Forward guidance raised |
| Guidance ↓ | Forward guidance lowered |
| EPS Rev ↑ | Consensus EPS estimates raised > 5% (90d) |
| EPS Rev ↓ | Consensus EPS estimates cut > 5% (90d) |
| Upgraded | Net analyst upgrades last 30 days |
| Downgraded | Net analyst downgrades last 30 days |
| Insider + | Net insider buying 6 months |
| Insider - | Net insider selling 6 months |

## Event Tags
Specific event type shown as individual tags:
| Tag Text | Trigger |
|----------|---------|
| Ruling | Pending court ruling |
| Investigate | Active investigation (non-SEC) |
| Hearing | Congressional/regulatory hearing |
| Lawsuit | Active lawsuit |
| Fraud | Fraud allegation (also triggers Kill Switch) |

## Tag Display
- Tags are shown as small text buttons/pills in the **Tags column** (last column before checkboxes)
- Clicking any tag opens a toggle panel showing all active tags with one-line explanations
- Tags are colored by category: Risk (Red-L), Opportunity (Green-L), Fundamental (Blue-L), Event (Orange-L)
- Max ~3 tags visible in column, "+N" overflow for additional

## Display Order in Tags Column
Kill Switch → Event → Risk → Fundamental → Opportunity

---

# 11. AI ASSESSMENT (Layer 2: Assessment)

Qualitative research layer. Part of the Assessment layer — generates contextual content without producing scores. See Three-Layer Framework in Section 1.

## Research Checklist (10 Items)
1. Guidance trend | 2. EPS revision trend (90d) | 3. Analyst rating changes (30d)
4. Insider activity (6mo) | 5. Relative strength vs sector (1M) | 6. Recent news (7d)
7. Short interest | 8. Competitive moat | 9. Valuation vs peers | 10. Upcoming catalysts (30d)

Items 1-7 feed Tags (Fundamental + Event tags). Items 8-10 are content-only (AI chat context).

## Output Sections

### Stock Expansion Card
- **Bulls 🟢:** 3-5 growth/moat/tailwind bullets
- **Bears 🔴:** 3-5 risk/headwind bullets
- **Buy Ideas / If Owned:** Removed from card. Handled through AI chat for dynamic, context-aware suggestions.

### MC Expansion Card
- **MC Summary:** One-line assessment + action
- **Bulls / Bears:** Market-level thesis (3-4 bullets each)
- **Buy Ideas (QQQ) / If Owned (QQQ):** Removed from card. Handled through AI chat.
- **Upcoming Events:** 📅 format, 7-day lookahead (Fed, NFP, CPI, earnings)

### Generation Rules
- Generates on Analyze button click
- Be decisive — specific prices, %, dates
- Reference SA score + tier in recommendations
- Reference MC score in market commentary
- Never override scores or contradict Zone Table signals

---

# 12. LIVE ANALYZER DASHBOARD

Main dashboard for daily analysis.

---

## Top Bar (Two Rows)

### Row 1 — App Header
- Left: App name/logo
- Right: Navigation menu (Analyzer | Lists | Score Matrix | Stock Matrix | MC Matrix | Optimizer | Settings)

### Row 2 — Action Bar
Left to right:
- **Watchlist dropdown** — select a saved watchlist to load symbols
- **Symbol input box** — type tickers comma-separated. When clicked and contains many symbols, expands into a **textarea** so all symbols are visible and editable. Shrinks back to single line on blur.
- **Upload icon** (📎) — import CSV/TXT/Excel file. If file contains multiple columns, **only extract readable ticker symbols** and ignore other data (prices, names, dates, etc.)
- **Analyze button** — runs SA + MC scoring. If no symbols entered, runs MC only.
- **Backtest button** — switches to backtest mode (date picker appears, forward return columns replace past performance)
- **Save icon** (💾) — save current analysis as named report, or save symbol list to watchlist (dropdown: "Save as Report" / "Save to Watchlist")

**When watchlist is loaded:** The symbol box populates with that list's symbols. The save icon offers "Update [watchlist name]" as an option.

**Chat screenshot capability:** User can paste a screenshot of a stock list into the chat box → AI extracts ticker symbols → populates the symbol input box for scoring.

---

## Analysis Table

When no analysis has been run, the table area shows: *"Enter symbols and click Analyze to start"*

If Analyze is clicked with no symbols, only the QQQ/MC row appears.

### Table Structure

```
┌─────────────────────────────────────────────────────────┐
│ HEADER ROW (column labels)                               │
├─────────────────────────────────────────────────────────┤
│ QQQ ROW (Market Conditions — always first)               │
├─────────────────────────────────────────────────────────┤
│ STOCK ROWS (scored stocks)                               │
│ ...                                                      │
├─────────────────────────────────────────────────────────┤
│ BETA ROW (averages + comparison to QQQ)                  │
└─────────────────────────────────────────────────────────┘
```

### Column Layout

| Column | Display | Click Toggle |
|--------|---------|-------------|
| Tier | Colored circle: T1/T2/T3/S | Tier criteria, max position, VIX adjustment |
| Symbol | Ticker (bold) + company name below (small, truncated) | Stock summary card |
| Score | SA colored circle + small faded previous score with ↑↓ arrow + 🔄 refresh | SA Score breakdown (question cards by category) |
| Tags | Text pills (max ~3 visible, "+N" overflow) | Tag card — all active tags with one-line explanations |
| MS | Colored rectangle badge showing both values (MS: 68 / C: 117) — colored by MS score using unified scale | MS calculation breakdown — formula, zone rule, signals |
| Price | Current price | Chart (full stock chart with all chart features) |
| 1D/5D/1M | % changes (Green-D / Red-D / Gray-D) | Detailed changes panel — all time periods including individual daily moves |
| ☐ | Checkbox | — |

All columns are sortable (click header).

### Checkbox Actions (top right of table)
When one or more checkboxes are selected, action buttons appear:
- **Stock Matrix** — run matrix for selected stocks
- **MC Matrix** — run MC matrix
- **Score Matrix** — always visible, defaults to all stocks in analysis. Selecting checkboxes filters to selected.
- **Add to Watchlist**
- **Remove from Watchlist**

### Price/Changes Toggle Panel (opens below row)
When Price or any performance column is clicked:
- **Current:** Price, Pre-market, Post-market
- **Performance bars:** 1D, 5D, 10D, 1M, 3M, 1Y — each with % and $ change
- **Individual daily moves:** Yesterday, 2 days ago, 3 days ago — each showing that single day's close-to-close change (NOT cumulative). Example: "2 days ago: -1.2% ($142.50 → $140.79)"
- **Full chart** for the stock below the price panel (all chart features apply)

---

## QQQ Row (First Row — Market Conditions)

The QQQ row replaces the old MC bar. Same column positions as stock rows but with MC-specific content:

| Column | QQQ Row Display | Click Toggle |
|--------|----------------|-------------|
| Tier | T1 circle | Cash allocation rules, VIX adjustment table |
| Symbol | "QQQ" | Full market detail card — MC summary, bulls/bears, QQQ chart with 50/200 SMA |
| Score | MC Score circle + previous score with ↑↓ arrow | MC Score breakdown (question cards: Technical, Macro, Breadth, Credit/Sentiment) |
| Tags | Market-level tags (Fed, CPI, earnings events, etc.) | Tag card with market event explanations |
| MS | C (Contrarian circle, inverted colors) | Zone table — which entry rule is active and why |
| Price | QQQ price | QQQ chart + pricing panel |
| 1D/5D/1M | QQQ performance (Green-D / Red-D) | Detailed QQQ changes panel |
| ☐ | Checkbox | — |

---

## BETA Row (Last Row — Summary & Comparison)

| Column | BETA Row Display |
|--------|-----------------|
| Tier | — |
| Symbol | "BETA" label |
| Score | Average SA score of all stocks in table |
| Tags | — |
| MS | Average MS of all stocks |
| Price | — |
| 1D/5D/1M | Two lines each: Average % of stock list (e.g., "+2.3%") AND ratio vs QQQ (e.g., "2.3×") |
| ☐ | — |

---

## Stock Summary Card (Symbol Click Toggle)

**Company Info:** Name, location, sector, established year, country
**AI One-Liner:** Single sentence summary of the stock (from AI Assessment)
**Key Metrics:** Market Cap, P/E, Net Margin, ROE, D/E, Next Earnings
**Growth Rates:** Cash Flow, OpIncome, Revenue (Annual + Quarterly), Momentum label
**AI Content Boxes:**
- Bulls 🟢 (Blue-L border) — 3-5 positive thesis bullets
- Bears 🔴 (Yellow-M border) — 3-5 risk factor bullets

**Latest News** section with "Show News" button
**Star rating** (1-5) for position quality

**Note:** Buy Ideas and If Owned sections are removed from the card. These are now handled through the AI chat box, which provides more dynamic, context-aware trading suggestions based on scores, MS signals, portfolio context, and current market conditions.

**Configurable Summary Metrics:** The Key Metrics and Growth Rates sections display fields defined in `display.md` config. User can add/remove/reorder which metrics appear (e.g., add Free Cash Flow Yield, remove Est. Year) via Settings.

---

# 13. AI CHAT ASSISTANT

An in-app Claude-powered chat interface that replaces the manual "export CSV → paste into Claude" workflow. The chat has full access to all app data and saves conversation threads.

## Architecture

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│  Chat UI     │────►│  Backend     │────►│ Claude API   │
│  (React)     │◄────│  (FastAPI)   │◄────│ (Anthropic)  │
└──────────────┘     └──────┬───────┘     └──────────────┘
                            │
                     ┌──────▼───────┐
                     │  Context     │
                     │  Builder     │
                     └──────┬───────┘
                            │
              ┌─────────────┼─────────────┐
              │             │             │
        ┌─────▼────┐ ┌─────▼────┐ ┌──────▼─────┐
        │ Scores   │ │ Matrices │ │  Configs   │
        │ & Alerts │ │ & Runs   │ │  & Rules   │
        └──────────┘ └──────────┘ └────────────┘
```

## Context Injection

When the user sends a message, the backend builds a context payload that is prepended to the Claude API call as a system message. The context includes:

### Always Included
- Current scoring config (SA questions, MC questions, point values)
- Current MS formulas and entry/exit rules
- Tier definitions
- Sector classifications

### Page-Aware Context (based on which page the chat is opened from)

| Page | Additional Context Injected |
|------|----------------------------|
| Analyzer | Current MC score + breakdown, all stock scores + flags, expansion card data |
| Score Matrix | Full matrix data for current date(s), forward returns, score buckets |
| Stock Matrix | Full history for current stock, all daily scores + prices |
| MC Matrix | Full MC history, QQQ price series, zone transitions |
| Optimizer | Correlation results, suggestion list, bucket analysis |

### On-Demand Context (user can request)
- "Show me the last 5 Score Matrix runs" → pulls from DB
- "Compare MU scores from October to January" → pulls Stock Matrix data
- "What does Q11 look like across all my saved dates?" → pulls cross-matrix data

## Chat Features

### Thread Management
- Threads are saved to database with timestamps
- Each thread has a title (auto-generated or user-named)
- Threads are tagged with the page/context they were started from
- User can browse, search, and resume past threads
- Delete individual threads or bulk delete

### Thread List View
- Accessible from chat panel sidebar or Lists page
- Shows: Title, date, page context, preview of last message
- Sort by date (newest first)

### Chat Commands (Action Triggers)

The chat isn't just for discussion — it can trigger real app actions:

| Command Example | Action |
|----------------|--------|
| "Analyze AAPL, MSFT, GOOG" | Runs SA scoring for those tickers, returns results in chat |
| "Run MC for today" | Runs MC scoring, returns breakdown in chat |
| "Score Matrix for my Love watchlist on 01/15/26" | Triggers Score Matrix run, returns summary |
| "Compare MU scores from Oct to Jan" | Pulls Stock Matrix data, summarizes trends |
| "Download the last Score Matrix as CSV" | Generates and provides download link |
| "Show Q11 correlation across all saved dates" | Pulls optimization data, shows analysis |
| "Add NVDA to my Love watchlist" | Modifies watchlist directly |
| "What's the current MC score?" | Returns latest MC if available, or runs fresh |

Commands are parsed by the backend. If a command requires an analysis run, the backend executes it, stores results, and returns the output through the chat response. The chat response includes both the data summary AND a link to the relevant page (e.g., "View full Score Matrix →").

### Suggested Prompts
When chat opens, show contextual suggestions based on current page:

**On Analyzer:**
- "Summarize today's market conditions and top picks"
- "Which stocks should I buy based on current scores?"
- "Compare my top 3 stocks — which has the best risk/reward?"

**On Score Matrix:**
- "Analyze false positives in this run"
- "Which questions contributed most to winners?"
- "What would scores look like if I removed Q16?"

**On Optimizer:**
- "Explain the top 3 suggestions"
- "What weight changes would improve win rate the most?"
- "Should I change the 1M hold period to 3 weeks?"

**On Stock Matrix:**
- "When was the best entry point for this stock?"
- "Does the score trend predict price moves for this stock?"

### Chat UI
- Docked at bottom of every page (collapsible panel)
- Expand to full-screen chat view
- Markdown rendering in responses (tables, code, bold)
- File attachment: Upload CSV for ad-hoc analysis
- Copy response button
- Export thread as markdown

## Claude API Configuration

- Model: `claude-sonnet-4-20250514` (or latest available)
- Max tokens: 4096 per response
- Temperature: 0.3 (factual/analytical bias)
- System prompt includes: scoring rules summary, current data context, user's optimization goals ("swing trading, 1M hold, reduce false positives")

---

# 14. HISTORICAL ANALYSIS MODE

The Analyzer has a "Backtest Mode" toggle that switches from live to historical analysis. This is the bridge between live analysis and the backtesting matrices.

## How It Works
- The Analyzer page has a **"Backtest"** button in the top bar
- Clicking it switches the dashboard to backtest mode:
  - A date picker appears for selecting the analysis date (not present in live mode)
  - The scoring engine fetches historical data AS OF that selected date
  - The past performance columns (1D, 5D, 1M, 3M, 1Y) are replaced with forward-looking return columns showing actual future price movement from that date
  - Results otherwise look identical to live Analyzer output

## Differences from Live Mode

| Feature | Live Mode | Backtest Mode |
|---------|-----------|---------------|
| Date | Today (auto) | User-selected past date |
| Data source | Current FMP data | Historical FMP data as-of-date |
| AI Assessment | Full | **Available if data exists for that date.** Earnings results, analyst ratings, insider activity, and other data points that can be confirmed as existing on/before the analysis date are shown. Data that cannot be verified for that date is excluded. |
| Fundamental Flags | Active | **Data-verifiable flags active.** Flags based on API data (EPS revisions, analyst upgrades/downgrades, insider buying/selling, earnings proximity) are shown if the data source confirms the date. News-headline-dependent flags that cannot be verified are excluded. |
| Risk/Sector/Opportunity Flags | Active | **Active.** All price-based and data-driven flags work (Downtrend, Volatility, Mean Reversion, Bounce Play, ETF badge, etc.) since they use historical price data. |
| AI Summary One-Liner | Generated | **Generated if sufficient data exists.** Must be accurate to that date — no forward-looking information leakage. |
| Forward Returns | N/A | Shows 5D / 1M / 3M columns with actual outcomes |
| AI Chat | Full context | **Full discussion allowed** — AI can discuss events, earnings, market conditions around that date using its training knowledge. Must clearly frame all discussion as "as of [date]" to avoid confusion with current conditions. |
| AI Tags | Active | **Active if assessable.** Uses whatever verifiable signals exist for that date. Shows no tags only if truly no data available. |

### Historical Data Accuracy Rules

**Core Principle:** Any data point that is verifiable as existing on or before the analysis date can be shown. Nothing from after the analysis date should influence the assessment (except forward returns, which are explicitly labeled as such).

**What IS available historically:**
- Earnings announcements that occurred before the analysis date
- Analyst ratings/upgrades/downgrades with dated timestamps
- Insider transactions with filing dates
- Price-based calculations (all momentum, trend, technical flags)
- Financial statements filed before the analysis date
- Short interest data (reported bi-monthly)
- Sector performance comparisons

**What is NOT available historically:**
- Real-time news headlines (unless from a dated API source)
- Guidance commentary (unless captured in earnings data)
- Subjective market sentiment that can't be verified

**AI Chat Historical Context:**
- The AI can discuss events around the analysis date using its training knowledge (e.g., "On Oct 3, 2025, the Fed announced..." or "AAPL had just reported earnings on...")
- The AI must clearly distinguish between "what was known on that date" vs "what we know now in hindsight"
- The AI should flag if it's uncertain whether information was public as of the analysis date
- Example good response: "As of Oct 3, 2025, MU had just reported Q4 earnings beating estimates by 12%. The stock was up 8% in the 5 days following. Your SA score of 71 correctly identified this as a strong buy."

## Forward Return Columns (Backtest Only)
When in Backtest mode, three additional columns appear in the stock table:
- **5D Fwd:** Actual return 5 trading days after the analysis date
- **1M Fwd:** Actual return 22 trading days after
- **3M Fwd:** Actual return 66 trading days after
- Color-coded green/red. Tooltip shows actual prices.
- Forward windows are configurable in General Settings.

## Saving to Matrices
After running a backtest analysis, the user can:
- **"Save to Score Matrix"** — saves all scored stocks for that date as a Score Matrix entry
- **"Add to Stock Matrix"** — saves individual stock's score to its Stock Matrix timeline
- **"Add to MC Matrix"** — saves the MC score to the MC Matrix timeline
- These are the entry points into the matrix system (Section 15).

### Default Naming
When saving any analysis (live or backtest), the default name is auto-generated:
- Format: `MM/DD/YY — [Watchlist Name]`
- Examples: `10/03/25 — Love`, `01/15/26 — Portfolio`, `12/01/25 — Tech Momentum`
- If no watchlist was used: `10/03/25 — 15 stocks` (count of symbols)
- User can edit the name before saving

### Trading Days Only
All date pickers across the app only allow selection of valid US market trading days:
- Weekends are disabled/grayed out
- US market holidays are disabled (New Year's, MLK, Presidents' Day, Good Friday, Memorial Day, Juneteenth, Independence Day, Labor Day, Thanksgiving, Christmas)
- If a non-trading day is entered manually or via API, the app automatically falls back to the **previous trading day's closing data** and shows a note: "Using 12/31/25 close (01/01/26 is a market holiday)"
- Intraday prices are not used — all analysis uses end-of-day closing prices

## Filing Lag Awareness
- Financial statements have ~45 day filing lag from quarter end
- The system uses the most recent filing available AS OF the backtest date
- Tooltip shows: "Financials as of Q3 2025 (filed 2025-11-14)" so the user knows which quarter's data was used

---

# 15. BACKTESTING MATRICES

Highest-priority feature. Three matrix views for validating and optimizing scoring accuracy.

**Overall Goal:** 60%+ win rate. High scores should correlate with positive forward returns. Win = positive 1M return.

---

## 5A. Score Matrix

**Purpose:** Compare SA scores to forward returns across multiple stocks on a SINGLE date. Primary tool for cross-stock validation.

### Header
- Back button, "Score Matrix (DATE)" title
- Backtest date picker with < > arrows
- Analyze button (single date)
- **Batch Run button** (multi-date — see below)
- "Rules →" link, CSV download button

### Table Columns

| Column | Source | Notes |
|--------|--------|-------|
| Symbol | Input | Clickable → Stock Matrix |
| Score | SA | Colored circle |
| MC | MC | Score for that date |
| MS-A | Formula A | User formula |
| MS-C | Formula B | Contrarian |
| Price | FMP | Price on analysis date |
| 5D | Calculated | 5-trading-day forward return |
| 1M | Calculated | 22-trading-day forward return |
| 3M | Calculated | 66-trading-day forward return |
| all SA questions | SA | Points + tooltip with raw data |

### Forward Returns
```
Return % = ((Forward Price − Base Price) ÷ Base Price) × 100
```

**Default windows:** 5D / 1M / 3M (configurable in General Settings as trading-day counts).

**Optimization default:** 1M is the primary optimization target. The optimizer correlates scores against 1M returns by default, but can be switched to any window via dropdown.

**Configurability:**
- **General Settings:** Define which forward windows exist app-wide (e.g., change 3M to 2M, add 6W)
- **Backtest dashboard:** One column is user-adjustable on the fly — a dropdown in the column header lets the user pick any trading-day count (e.g., 15, 30, 45, 66) without changing the config. This "custom" column sits alongside the default windows.
- **Config file:** `master-score.md` defines the default optimization window (1M = 22 trading days). The optimizer reads this.

**Loading:** 1M auto-loads. Other windows load on-demand (click refresh icon on column header).

### Multi-Date Batch Runs ⭐ CRITICAL FOR OPTIMIZATION

Run the same stock list across multiple dates simultaneously.

**UI:** "Batch Run" button → modal:
- Select dates: Checkboxes from saved dates, OR date range picker (e.g., every Monday in Q4 2025)
- Select stock list: Dropdown of saved watchlists
- Run all stock × date combinations

**After batch run, show aggregated summary view:**
- **Score Buckets:** Win rate + avg return for 70+, 60-69, 50-59, <50
- **Per-Question Correlation:** Each all SA questions correlated with 1M return across all dates
- **False Positive Rate:** % of scores ≥60 with negative 1M
- **False Negative Rate:** % of scores <50 with positive 1M > 10%
- **MC Zone Breakdown:** Performance grouped by MC zone at time of scoring

---

## 5B. Stock Matrix

**Purpose:** Track one stock's SA score and price over time. Shows score-to-price trend correlation.

### Header
- Symbol dropdown + search, Price bar (current + 1D/5D/1M/3M/52W changes)
- Add Dates, download, refresh buttons

### Stats Bar
- Date range, Total days, Avg Price, Avg Score, Avg MC

### Chart
All chart customization from Section 16A applies (chart types, score color coding, line styling, navigation, visibility toggles). Defaults:
- Price: Candlestick or Heikin-Ashi (user choice, left Y-axis), with 50 SMA and 200 SMA lines
- Volume bars (toggleable sub-chart)
- SA Score bars: Default green/red by direction, OR score color coding mode (bars colored by unified score scale — Green-L for ≥70, Teal-D for 60-64, etc.) — right Y-axis 0-100
- Toggleable overlays: MC line, VIX line, MS-A line, MS-C line, Bollinger Bands
- Time ranges: 1M / 3M / 6M / 1Y / 2Y / 5Y / ALL
- Filter Spikes toggle

### Table Columns
Date | Score | MC | VIX | MS-A | MS-C | Price | 1M Fwd | Updated | all SA questions

---

## 5C. MC Matrix

**Purpose:** Track daily MC scores against QQQ. Validate MC as market timing tool.

### Header
- Total Days, Date Range, Avg Score, Add Dates, download, refresh

### Stats Bar
- QQQ: Price, 1D, 5D, 30D, 3M, 1Y changes

### Chart
All chart customization from Section 16A applies (chart types, score color coding, line styling, navigation, visibility toggles). Defaults:
- QQQ price: Candlestick or Heikin-Ashi (user choice), with 50 SMA and 200 SMA lines
- VIX line: Orange, dashed
- MC score bars: Default green/red by direction, OR score color coding mode (bars colored by MC zone — Red-M for ≥75, Orange-M for 60-74, Green-D for 50-59, etc.)
- Time ranges: 3M / 6M / 1Y / 2Y / 5Y / ALL
- Toggles: QQQ / VIX / MC / 50 SMA / 200 SMA / Bollinger Bands visibility
- Amplify: Scale adjustment for bar height

### Table Columns
Date | MC | QQQ | VIX | 5D | 1M | 3M | Updated | all MC questions

---

# 16. OPTIMIZATION ENGINE

The suggestion engine. Analyzes correlation between individual questions and forward returns, then suggests weight changes.

## Data Source
Reads from ALL saved Score Matrix runs in the database. More dates + more stocks = better analysis.

### Data Gap Detection
Before running optimization, the engine scans saved matrix data for gaps:
- **Missing dates within a range:** e.g., 3 months of MC data but one week missing in the middle
- **Missing stocks in batch runs:** e.g., a watchlist of 30 stocks but only 25 scored on one date
- **Stale data:** Entries scored with an old config version

Alert format: "⚠️ Gap detected: Oct 13-17, 2025 missing from MC Matrix. Run these dates to improve optimization accuracy? [Run Now]"

The user can choose to fill gaps before optimizing, or proceed with incomplete data (results will note the gap).

### Multi-Period Analysis
The optimizer defaults to 1M forward returns, but supports comparing multiple periods simultaneously:

**Side-by-side view:** Run correlation analysis for 5D, 1M, and 3M at the same time. Three columns of results showing which questions predict best at each timeframe.

**Combined/averaged view:** Select up to 3 forward periods and average their correlation coefficients into a single combined score. Example:
- Q11 correlation: 5D = 0.08, 1M = 0.19, 3M = 0.15 → Combined avg = 0.14
- This answers: "Which questions predict well across ALL timeframes, not just one?"

**Pattern detection:** Visual comparison helps spot anomalies like "5D shows 80% win rate for 70+ scores, but 1M drops to 55%." This suggests short-term momentum fades — the AI chat can discuss why and suggest hold period adjustments.

## Core Analytics

### 7A. Per-Question Correlation Dashboard

For each SA question (all SA questions):
- **Correlation coefficient** with 1M return across all saved stock-date records
- **Visual:** Bar chart or heatmap showing all SA questions correlation strength
- **Color:** Green (positive correlation > 0.1), Gray (neutral ±0.1), Red (negative correlation < -0.1)
- **Sort:** By correlation strength (strongest first)

### 7B. Score Bucket Analysis

| Bucket | Metrics Shown |
|--------|---------------|
| 70+ (Elite) | Win rate, avg 1M return, count, false positive % |
| 60-69 (Strong) | Same |
| 50-59 (Average) | Same |
| < 50 (Weak) | Same |

Show as summary cards + bar chart. Target: monotonically increasing win rate as score increases.

### 7C. Question Impact Analysis

For each question, show:
- **Contribution when positive:** Avg 1M return for stocks where this Q scored high
- **Contribution when negative:** Avg 1M return for stocks where this Q scored low/negative
- **Spread:** Difference between high-scorers and low-scorers for this question
- **Suggestion:** If spread is large → "High impact, keep or increase weight." If spread ≈ 0 → "Low impact, consider reducing weight." If spread is negative → "⚠️ Reverse correlation — this question may be hurting accuracy."

### 7D. MC Zone Breakdown

- Win rate of BUY signals by MC range (< 30, 30-49, 50-59, 60+)
- QQQ forward returns by MC zone
- Validate: Does "no new buys" at MC 60+ actually avoid losses?

### 7E. Suggestion Panel

After analysis runs, generate a ranked list of suggestions:
```
1. ⬆️ Q11 (Momentum Magnitude): Correlation 0.19 — strongest predictor. Consider increasing max from 3 to 5.
2. ⬇️ Q16 (Country): Correlation 0.01 — near zero impact. Consider removing or reducing to 0 pts.
3. ⚠️ Q22 (Sector Preference): Correlation -0.05 — slightly negative. Review sector assignments.
4. 🔄 Q5 (1M Price Change): Consider testing 3-week instead of 1-month window.
5. ✅ Q4 (Net Margin): Correlation 0.12 — solid predictor. Keep current weight.
```

### 7F. Hold Period Analysis

Run the same correlation analysis but varying the forward return window:
- 5D forward returns
- 1M forward returns (default)
- 3M forward returns

Show which hold period has strongest score-to-return correlation. This answers: "Is 1 month the right swing trade window?"

### 7G. Comparison View

When the user modifies weights in the config, show side-by-side:
- **Before:** Win rates and returns with old weights
- **After:** Win rates and returns with new weights (recalculated from stored raw question points)
- **Delta:** Improvement or regression per bucket

This allows the user to test weight changes against historical data without re-running the full backtest.

---

# 17. AUTO-OPTIMIZER SCAFFOLD (Future)

**Build as inactive scaffold only.** Wire up the UI and database hooks but don't implement the optimization loop yet. This allows activation later without restructuring.

## Concept
A "Run Optimization" button that:
1. Takes all saved Score Matrix data
2. Tests thousands of weight combinations for all SA questions
3. Finds the set that maximizes win rate + avg return while minimizing false positives
4. Shows before/after comparison
5. User accepts or reverts

## Scaffold Requirements
- Database table: `optimization_runs` (id, date, config_before, config_after, metrics_before, metrics_after, status)
- API endpoint: `POST /api/optimize` (returns 501 Not Implemented for now)
- Frontend: "Auto-Optimize" button on Optimizer page (grayed out with "Coming Soon" tooltip)
- Settings toggle: "Enable Auto-Optimizer (Beta)" — defaults to off

## Future Implementation Notes
- Use scipy.optimize or genetic algorithm approach
- Constraint: No question weight can exceed 2× its default
- Constraint: Total max score must remain ≤ 100 (renormalize)
- Guard against overfitting: Use walk-forward validation (train on earlier dates, test on later)
- Maximum runtime: 60 seconds per optimization run

---


---

# 18. ENTRY & EXIT OPTIMIZATION (Future)

> **Status:** Not built. To be developed AFTER the scoring system is validated and the basic optimizer proves that scores correlate with returns.

## Premise
Sections 1-17 answer: "Can we identify good stocks?" This section answers: "Once we identify them, when exactly do we buy and sell?"

## Concept
Instead of measuring raw forward returns ("what was the price in 1M?"), simulate realistic trade management using score changes, timing, and goals.

### Entry Optimization
- **Score-change triggers:** Buy after SA increases by X points in Y days
- **MC-timed entries:** Buy when MC crosses above a threshold
- **MS signal entries:** Buy on specific MS zone transitions (e.g., SELECTIVE → BUY)
- **Dip-buy entries:** Buy when SA ≥ 70 but price drops X% (score stays high, price drops = opportunity)
- Test: which entry approach yields the best 1M/3M returns?

### Exit Optimization
- **Target exit:** If stock hits +20% before end of hold period, mark as exited at +20%
- **Stop loss exit:** If stock hits -10%, exit at -10%
- **Trailing stop:** If stock hits +15% then pulls back 5% from peak, exit at peak -5%
- **Score deterioration exit:** If SA drops below X, exit regardless of price
- **MC deterioration exit:** If MC drops below X, exit all positions
- **Time-based:** Hold exactly N days regardless
- Test: which exit strategy maximizes risk-adjusted returns?

### Combined Entry + Exit
- Pair specific entry triggers with specific exit strategies
- Build a matrix of entry × exit combinations and backtest each
- Find the optimal pairing for different score ranges and market conditions

### Why Wait
- Requires validated scoring system first (if scores don't predict returns, entry/exit timing won't help)
- Needs daily high/low data for intraday target/stop detection
- Complex simulation engine — separate from the scoring optimizer
- Should only be built once the user has confidence in the base system

### Prerequisites Before Building
1. Score-to-return correlation > 0.3 confirmed across multiple periods
2. Win rate for 70+ scores > 65% confirmed
3. Basic optimizer (Section 16) functioning and producing useful suggestions
4. Sufficient historical data saved (minimum 6 months of matrix data)

# APPENDIX A: VALIDATION METRICS

## Target Performance

| Metric | Target |
|--------|--------|
| Hit Rate (BUY signals) | > 65% positive 1M |
| Avoid Accuracy | > 60% negative 1M |
| Score-Return Correlation | > 0.50 |
| Alpha vs QQQ | Positive |

## Score Bucket Targets

| Bucket | Win Rate | Avg 1M |
|--------|----------|--------|
| ≥ 70 | > 75% | > +15% |
| 60-69 | > 65% | > +10% |
| 50-59 | > 50% | > 0% |
| < 50 | < 45% | Negative |

## MC Zone Expected QQQ Performance

| MC Zone | Expected 1M |
|---------|-------------|
| < 30 | Strong positive |
| 30-49 | Positive |
| 50-59 (Neutral) | Mixed |
| 60+ | Flat to negative |

---

# APPENDIX B: BACKTEST RESULTS (V11 Reference)

Historical performance data to validate against:

| Score | Count | Win Rate | Avg 1M Return |
|-------|-------|----------|---------------|
| 70+ | 7 | 86% | +9.2% |
| 60-70 | 56 | 59% | +6.1% |
| 50-60 | 113 | 60% | +4.5% |
| < 50 | 48 | 42% | +0.0% |

Based on 224 stock-date observations across 6 dates (July-Dec 2025). Excluded: Crypto and Pharma/Biotech.

---

**END OF BUILD PROMPT**
