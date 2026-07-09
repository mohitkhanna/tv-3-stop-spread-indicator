# Project Rules: TradingView 3-Stop Spread Indicator

This file provides context and style guidelines for any Google Antigravity AI agent working on the TradingView 3-Stop Spread indicator project in this workspace.

---

## 1. Project Context
This indicator implements swing trader Jeff Sun's core risk management and stop-loss trailing system in **Pine Script v6**. The primary goal is to help a trader calculate and visualize split stops, manage profit-taking targets, handle time-based exits, and apply mental trailing stops with opening range breakout logic.

### Key Artifacts
- **[README.md](file:///Users/mkhanna/projects/trading/tv-indicator-3-stop-spread/README.md):** The master specification document detailing the description, key features, settings, mathematical logic, and nuances.
- **[implementation_plan.md](file:///Users/mkhanna/.gemini/antigravity/brain/d585e825-d50a-4c5d-a108-5ef38e27b882/implementation_plan.md):** The active implementation strategy approved by the user.

---

## 2. Core Mathematical Formulas

For Entry Price $E$, Final Stop Price $S$, and Position Size $N$ (with risk $R = |E - S|$):

- **Stop Level 1 (33% risk):** $S_1 = E \mp \frac{1}{3}R$ (Qty: 33%)
- **Stop Level 2 (66% risk):** $S_2 = E \mp \frac{2}{3}R$ (Qty: 33%)
- **Stop Level 3 (100% risk):** $S_3 = S$ (Qty: 34%)
- **2R Shave Target:** $T_{2R} = E \pm 2R$ (shave 33% of position)
- **4R Breakeven Consolidation Target:** $T_{4R} = E \pm 4R$ (consolidate stops to breakeven $E$)

---

## 3. Implementation Rules for Future Agents

When modifying or updating the indicator, adhere to the following:
1.  **Pine Script Version:** Always use `//@version=6`.
2.  **Naming Conventions:** Use camelCase for variables/functions and ALL_CAPS for constants.
3.  **Strict Boolean Logic:** Follow Pine Script v6 guidelines; compare values explicitly (e.g. `x != 0`) rather than treating integers as booleans.
4.  **UI & Aesthetics:**
    *   Maintain a high-contrast HUD table showing exact Stop levels, Sell Quantities, and Remaining Size at a glance.
    *   Use clean, neon, or classic colors for lines and labels to ensure modern design standards on dark and light themes.
5.  **Timeframe Security:** When requesting opening range values from lower timeframes, handle security requests safely using user-defined functions or tuppled variables, and always support custom ORL timeframes ("5", "10", "15", "30") and manual overrides.
