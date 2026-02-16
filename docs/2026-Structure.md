# Module-1: Structure

**Lovable Trades V17 - Feb 16, 2026**

Lovable Trade App is a market and analysis tool to find best stocks, best timing and best method of trading and investing and money management. The App is built on 4 core modules designed independently with their own Vault page, and database table and dedicated code page.

## The 4-Module Hierarchy
1. **Module-1: Structure** → Architecture, navigation logic, and module communication.
2. **Module-2: Analysis** → Stock Analysis (AS) scoring engine, score-specific color/title mapping, and historical data storage.
3. **Module-3: Market** → Market Condition (MC) scoring and market-specific color/title mapping.
4. **Module-4: Signals** → Action signals (Analysis + Market + Alerts) and alert-specific logic.

**Design** → Global CSS variables, hex definitions (e.g., t-blue), and UI/UX layout rules. The Design Module is managed via 'Design' page. 

## Data Flow & Communication
1. FMP API provides raw data to Analysis and Market modules.
2. Analysis & Market modules calculate scores and assign "Global Color Names" (e.g., t-green).
3. Signals module monitors results from both and triggers Alerts if specific thresholds are met.
4. Design module maps "Global Color Names" to specific Hex codes for final rendering.

## System Rules
- **No Hardcoded Colors:** Logic modules (2, 3, 4) must only use color aliases.
- **Consolidation:** Alerts are handled within Signals. History is handled within Analysis/Market storage.
- **Independence:** Each module has a dedicated database table (vault_structure, vault_analysis, etc.).

### Persistence & Documentation Rules
- All architectural decisions must be mirrored in the Vault text before closing a session.
- The Chat Assistant acts as a sounding board, but the Vault content is the "Source of Truth."
- If a session is interrupted, refer to the last "End of Structure" marker to resume.

## End of Structure
