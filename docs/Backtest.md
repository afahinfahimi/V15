# Backtest Page — V15

## Purpose
Simulate what scores and Action Signals would have been generated on any past trading date, then compare against actual future price movements to measure prediction accuracy.

## Column Layout
Symbol | SA | Action | Alert | MC | VIX | Price | 1D P | 5D | 1M | 3M | Tier | 10D F | 1M F | 2M F

- **Past performance** columns (1D P through 3M) show returns UP TO the analysis date
- **Future performance** columns (10D F, 1M F, 2M F) show returns AFTER the analysis date
- QQQ Market Reference row shows benchmark returns + alpha multipliers

## Action Signal Integration
- The Action column uses V15 Signal Engine rules (same as Dashboard)
- Buy/Sell/Hold signals are calculated using the SA score, MC score, and VIX from the backtest date
- MC streak detection (≥70 for 3+ days) queries mc_matrix_results for consecutive readings

## Data Sources
- **stock_matrix_results**: Historical SA scores, raw_data snapshots, and sa_details per symbol/date
- **mc_matrix_results**: Historical MC scores, VIX, and QQQ price per date
- **FMP API**: Historical price data for future performance calculations
- **saved_backtests**: Persisted backtest snapshots (loaded via URL ?load=id)

## Scoring Behavior
- SA scores are recalculated from the raw_data snapshot stored on the analysis date
- MC score is the exact value recorded on the analysis date
- MS1–MS4 variants are calculated from SA + MC + Momentum (historical price changes)
- Tiers use SA score + profitability + market cap from the snapshot
- Action Signals evaluate SA, MC, VIX, and MC streak for that date

## Session & Persistence
- Results are saved to sessionStorage for quick navigation
- Backtests can be named and saved to the saved_backtests table
- Snapshot naming: "MMM d, yyyy - [Watchlist] (X stocks)"
- Load via Lists page or URL parameter (?load=uuid)

## Rules & Constraints
- Analysis date must be a trading day (weekends/holidays skipped to nearest)
- Future performance dates also snap to nearest trading days
- Calendar navigation uses arrow buttons (←/→) for day-by-day stepping
- Force-refresh button (🔄) re-runs analysis for current date
