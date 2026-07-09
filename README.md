# TradingView 3-Stop Spread Indicator (Pine Script v6)

This repository contains a TradingView indicator written in **Pine Script v6** designed to implement the core 3 Stop Loss calculations and risk management framework shared by swing trader Jeff Sun.

The primary focus of this indicator is to help a trader quickly calculate the 3 split stop-loss levels and their corresponding share quantities based on entry conditions, position size, and stop parameters. The rules of the framework are displayed as reference guidelines inside the dashboard widget.

---

## 1. Description

Jeff Sun's strategy improves the trader's equity curve and reduces monthly drawdowns by segmenting the initial risk into three levels. Rather than risking a full 1R loss at the final stop, the position is split into thirds. The stop losses are staggered, bringing the initial blended risk down to approximately **0.67R**.

The indicator plots the Entry Price and the 3 calculated Split Stop Loss levels on the chart. It also features a customizable dashboard table that displays the exact prices, share quantities to sell at each level, and the trading days since entry. It does not draw indicators or execute trailing logic on the chart; instead, it serves as a calculator and a visual checklist for the trader.

---

## 2. Key Features

- **Split Stop Loss Visuals:** Plots the calculated horizontal lines for the Entry Price, Stop Level 1 (33% risk), Stop Level 2 (66% risk), and Stop Level 3 (100% risk / Final Stop).
- **Flexible Entry Price Selection:** Allows selecting from quick defaults (Current Close, High of Current Bar, Low of Current Bar, Open of Current Bar) or manually specifying the price.
- **Dynamic Final Stop Price (SL) Options:** Select standard SL levels automatically (Low of Day, Low of Entry Bar, Low of Previous Bar, Swing Low, Moving Average) or override with a manual price.
- **Trade Days Tracker:** Counts and displays the number of daily sessions since entry inside the dashboard table (no chart drawings for day tracking).
- **Interactive HUD Table:** Displays real-time statistics in a customizable table (supporting all 4 corners, plus middle left and right):
  - Current Trade Day
  - Active Stop Levels, Prices, and Shares to Sell
  - Reference rules for Profit-Taking (2R shave, 4R breakeven consolidation), Day 3 consolidation, and Day 4+ MA/ORL mental stops.
- **Aesthetic Customization:** Comprehensive control over table colors, frame/border widths, text color, and various text sizes (auto, tiny, small, normal, large, huge).

---

## 3. Indicator Settings & Inputs

### Trade Configuration
- **Trade Direction (Long/Short):** Dropdown to select if the trade is a buy or sell setup (default: Long).
- **Entry Date & Time:** Time picker to define when the trade was executed (default: current bar's timestamp).
- **Entry Price Source:** Dropdown (Manual Override, Close of Entry Bar, High of Entry Bar, Low of Entry Bar, Open of Entry Bar).
- **Manual Entry Price:** Override price if "Manual Override" is selected.
- **Stop Price Source:** Dropdown (Manual Override, Low of Day, Low of Entry Bar, Low of Previous Bar, Swing Low, Moving Average). Default: Low of Day.
- **Manual Stop Price:** Override price if "Manual Override" is selected.
- **Position Size (Shares):** Total number of shares/contracts purchased (default: 3).

### Moving Average Configuration
- **MA Type:** Choose Simple (SMA) or Exponential (EMA) for the generic MA stop source (default: SMA).
- **MA Length:** Set the length of the generic MA (default: 20).

### Sizing and Rounding
- **Allow Fractional Shares:** Boolean toggle (default: false). If disabled, share quantities are rounded to whole numbers. Note: The price levels themselves are never rounded and remain exact.

### Aesthetics & Customization
- **Show Dashboard Table:** Enable or disable the HUD table.
- **Dashboard Position:** Dropdown covering Top Left, Top Right, Middle Left, Middle Right, Bottom Left, Bottom Right.
- **Table Text Size:** Text size selector (Auto, Tiny, Small, Normal, Large, Huge).
- **Color Palette:** Custom inputs matching the user's styling palette.

---

## 4. Mathematical Logic & Formulas

Let $E$ be the calculated Entry Price, $S$ be the calculated Final Stop Price, and $N$ be the initial Position Size. Let the risk be $R = |E - S|$.

### Stop Staggering
1.  **Stop Level 1 (33% of risk distance):**
    $$S_1 = E \mp \frac{1}{3}R$$
    *Quantity to sell:* $q_1 = \text{round}(N \times 0.33)$ (or exact float if fractional shares enabled)
2.  **Stop Level 2 (66% of risk distance):**
    $$S_2 = E \mp \frac{2}{3}R$$
    *Quantity to sell:* $q_2 = \text{round}(N \times 0.33)$
3.  **Stop Level 3 (100% of risk distance - Final Stop):**
    $$S_3 = S$$
    *Quantity to sell:* $q_3 = N - q_1 - q_2$ (ensures exact total position size is covered)

*Note: $\mp$ and $\pm$ symbols correspond to Long (upper sign) and Short (lower sign) trades.*

### Blended Risk Calculation
Instead of a standard 1R risk, the expected loss if all stops are hit sequentially without profit taking is:
$$\text{Blended Risk} = (q_1 \times (E - S_1)) + (q_2 \times (E - S_2)) + (q_3 \times (E - S_3)) \approx 0.67 \times (N \times R)$$

---

## 5. Reference Rules Displayed in Table

- **2R Shave Rule:** If profit exceeds $E \pm 2R$ on Day 0-2, shave 33% of position. Adjust remaining stop size.
- **4R Override Rule:** If profit exceeds $E \pm 4R$ before Day 4, consolidate all risk to breakeven stop ($E$).
- **Day 3 Exit:** Shave 33% of remaining position. Consolidate all stops to breakeven ($E$).
- **Day 4+ Trailing:** Mental stop on 10-MA. If a daily close is below the MA, set stop to 5-minute ORL for the next session. Reset to breakeven if not triggered by EOD.
