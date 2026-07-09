# Implementation Plan: Jeff Sun's 3-Stop Spread Indicator (Pine Script v6)

This document outlines the final implementation plan for the TradingView indicator in Pine Script v6, focusing strictly on split stop calculations and reference rule display.

---

## Refined Scope & Requirements

Following user feedback:
1.  **Core Focus:** The indicator will only calculate and plot the 3 Split Stop Loss levels and the Entry Price line on the chart.
2.  **No Active Logic for Triggers/Exits:** We will not build trailing logic, active ORL fetching, or profit-taking crossovers.
3.  **Reference Guidelines:** The HUD table will display the profit-taking, day 3, and day 4+ MA rules as static text guidelines for the trader to reference.
4.  **Day Tracker:** The widget table will display the trading days since entry (based on bar calculations since the entry timestamp) but will not draw daily markers on the chart.
5.  **Aesthetics:** Table colors are fully customizable using the user's styling palette. Text size is adjustable (auto, tiny, small, normal, large, huge). Positions support all 4 corners, plus middle left and right.

---

## Detailed Specifications & Inputs

### 1. Trade Settings
*   **Direction:** Long / Short dropdown (default: Long).
*   **Entry Timestamp:** Time picker (default: current bar's time if left at default).
*   **Entry Price Source:** Dropdown with options:
    *   `Manual Override` (uses the `Manual Entry Price` input)
    *   `Close of Entry Bar` (default)
    *   `High of Entry Bar`
    *   `Low of Entry Bar`
    *   `Open of Entry Bar`
*   **Manual Entry Price:** Float input (default: 0.0) used if `Manual Override` is active.
*   **Stop Price Source:** Dropdown with options:
    *   `Manual Override` (uses the `Manual Stop Price` input)
    *   `Low of Day` (default - calculated as the daily low of the entry day session)
    *   `Low of Entry Bar`
    *   `Low of Previous Bar`
    *   `Swing Low` (lowest low of the last 10 bars relative to the entry bar)
    *   `Moving Average` (calculates the value of the user's configured MA on the entry bar)
*   **Manual Stop Price:** Float input (default: 0.0) used if `Manual Override` is active.
*   **Position Size (Shares):** Int/float input (default: 3).

### 2. Moving Average Configuration
*   **MA Type:** Dropdown option to choose `SMA` or `EMA` (default: `SMA`).
*   **MA Length:** Integer input (default: `20`).

### 3. Sizing & Rounding
*   **Allow Fractional Shares:** Boolean toggle (default: false).
    *   If false, share quantities at Stop 1 (33%), Stop 2 (33%), and Stop 3 (34%) are rounded to the nearest integer.
    *   If true, share quantities display as floats with up to 2 decimal places.
    *   *Note:* The price levels $S_1, S_2, S_3$ themselves are never rounded.

### 4. Display Styles
*   **Dashboard Position:** Dropdown:
    *   Top Left, Top Right, Middle Left, Middle Right, Bottom Left, Bottom Right.
*   **Text Size:** Dropdown:
    *   `size.auto`, `size.tiny`, `size.small`, `size.normal`, `size.large`, `size.huge`.
*   **Color Palette:** Standardized color inputs utilizing the user-provided styling block.

---

## Mathematical Logic

Let $E$ be the calculated Entry Price, $S$ be the calculated Final Stop Price, and $N$ be the initial Position Size. Let the risk be $R = |E - S|$.

*   **Stop Level 1 (33% risk):** $S_1 = E \mp \frac{1}{3}R$ (Qty: $q_1 = \text{round}(N \times 0.33)$)
*   **Stop Level 2 (66% risk):** $S_2 = E \mp \frac{2}{3}R$ (Qty: $q_2 = \text{round}(N \times 0.33)$)
*   **Stop Level 3 (100% risk):** $S_3 = S$ (Qty: $q_3 = N - q_1 - q_2$)

---

## Verification Plan

### Automated Checks
*   Verify Pine Script compilation.

### Manual Checks
*   Add the indicator to a chart and verify:
    1.  Stops and entry lines draw correctly.
    2.  The HUD table shows the correct quantities, prices, elapsed day count, and reference rules.
    3.  Toggling settings (such as text size, position, and colors) updates the table instantly.
