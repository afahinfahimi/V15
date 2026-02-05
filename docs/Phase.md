
Phase 2: UI Detail Refinement (Master Scores & Zones)
Goal: Update the expanded stock view to specifically display the V13 Master Score formulas (Formula A vs. Formula B) and the conditional "Zone Logic" (e.g., VIX Override rules).
Tasks:
Redesign the StockTable expanded row to show a side-by-side comparison of Master Score A and Master Score B.
Implement the specific color coding for Market Zones (Panic = Black, Buy = Green, etc.) in the MarketBar with the specific text instructions (e.g., "Stand down, SA unreliable").
Add the "Tier Classification" breakdown (showing why a stock is T1/T2/T3 based on Margin/Cap).
Phase 3: Real Data Integration (FMP Service)
Goal: Replace the random mockData with a real service layer that fetches data from Financial Modeling Prep (FMP).
Tasks:
Create services/fmpService.ts.
Implement fetching logic for Quotes, Profiles, Financial Statements, and Technical Indicators.
Map FMP API responses to the StockData interface used by the scoring engine.
Note: This will require a valid FMP API key to function.
Phase 4: Watchlist Management
Goal: Allow users to save and manage custom watchlists (persistence).
Tasks:
Add "Save Watchlist" button and modal.
Implement local storage (or Supabase client setup) to persist custom lists.
Update the specific "Sector" and "ETF" lists based on V13 requirements.
Phase 5: Mobile Responsiveness & Final Polish
Goal: Ensure the complex data tables (Matrices) are usable on mobile devices.
Tasks:
Make StockMatrix and ScoreMatrix horizontally scrollable with sticky columns (already partially done, needs refining).
Optimize font sizes and spacing for the "Master Score" badges.
Final code cleanup.
