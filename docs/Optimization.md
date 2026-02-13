# Optimization Page — V15

## Purpose
Research hub for improving scoring formula accuracy. Analyze historical score-to-return correlations, track formula iterations, and validate changes before implementing.

## Tabs

### Scores
- Groups historical results by score ranges (80–84, 85–89, 90–94, 95–100)
- Calculates win rate (% positive returns) for each range
- Supports return periods: 5D, 1M, 3M
- Three analysis models: SA-only, MS1 (60/30/10), MS2 (equal weight)
- Best-performing range marked with 🏆
- Clickable "Count" values open drill-down dialogs showing individual stocks

### Data
- Shows count of matrix entries per month in a grid format
- Helps identify data gaps or thin sample sizes
- Two-column master grid layout with 50/50 split

### SA Breakdown
- Correlates individual SA questions (Q1–Q30) with future returns
- Identifies which questions are most predictive
- Helps prioritize which scoring factors to adjust

### MC Breakdown
- Same correlation analysis for MC questions
- Shows how market environment factors affect prediction accuracy

### Ideas
- Capture raw optimization hypotheses
- Status workflow: raw → formatted → testing → tested → implemented/rejected
- Categories: sa_scoring, mc_scoring, combo, timing, hold_period, other

### Log (Changelog)
- Records implemented formula changes with version labels
- Tracks before/after win rates and delta
- Links to related ideas for traceability

### App (Pro Advisor)
- Architectural planning chat pre-loaded with tech stack + schema summary
- Stateless — no persistence, grows vertically with conversation

## Data Sources
- **stock_matrix_results**: SA scores + price returns for score analysis
- **mc_matrix_results**: MC scores + market returns for MC analysis
- **optimization_ideas**: Idea backlog (raw_idea, formatted_hypothesis, status)
- **optimization_changelog**: Change history (version_label, before/after win rates)

## Key Definitions
- **Win Rate**: % of stocks with positive returns in the selected period
- **Score Range**: 5-point buckets (80–84, 85–89, etc.)
- **Alpha**: Stock return relative to QQQ benchmark return
