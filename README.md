# gap-up-short-backtester
An event-driven quantitative backtesting framework in Python designed to evaluate and optimize delayed gap-up short strategies on equities using Interactive Brokers historical data.

# Systematic Backtesting: Shorting Extreme Overnight Gap-Ups

## 1. Methodology & Data Collection
The core objective of this backtest was to determine whether extreme overnight gap-ups serve as a statistically viable short setup within the recent market regime. To isolate a potential edge, US equities listed on the NASDAQ and NYSE were tracked over a **six-month period**, focusing exclusively on high-momentum catalysts where a stock opened **>= 10%** higher than its prior day's close.

The infrastructure was built using a three-step pipeline:
* **Data Ingestion:** Automated extraction of daily historical screener data directly from FinViz via a Python script to capture all equities hitting the 10% threshold.
* **Storage:** Aggregation of raw data into a local **DuckDB** database to establish a high-performance foundation for query execution.
* **Historical Sourcing:** Programmatic connection to the **Interactive Brokers API** to retrieve high-resolution historical price data for each screened ticker, ensuring institutional-grade accuracy.

---

## 2. Exploratory Data Analysis (EDA)
Correlation and regression analyses were conducted across a wide array of features to determine if specific variables could reliably predict intraday price action (execution returns). 

The following metrics were cross-examined:
* **Gap Characteristics & Technicals:** Gap vs. open-to-close, open-to-high, and open-to-low; gap date vs. open-to-close; z-score gap and Rate of Change (ROC) vs. open-to-close.
* **Market Structure & Fundamentals:** Float size vs. open-to-close/open-to-high; short interest vs. open-to-high/open-to-low; market cap, prior performance, and sector vs. open-to-close.
* **Temporal Dynamics:** Cumulative return tracking across various holding periods to isolate the optimal exit window.

> **Conclusion:** No statistically significant linear relationships emerged across any of these individual variables. Macroscopic correlations failed to yield a simple linear edge.

---

## 3. Parameter Tuning & Optimization Framework
Due to the lack of linear correlations, the approach shifted to empirical optimization. Using a dedicated training subset, bucket tests were executed across a wide spectrum of **Stop Loss (SL)** and **Take Profit (TP)** brackets alongside variations in holding periods.

To avoid curve-fitting, parameter permutations were evaluated against a comprehensive risk-reward matrix tracking:
* Win Rate & Median Risk-to-Reward (R:R) for mathematical expectancy.
* Maximum Drawdown & Max Risk to assess structural pain tolerance.
* Realistic, per-trade transaction fees to account for execution friction and slippage.

---

## 4. Empirical Results & Performance Dashboard

### Core Performance Metrics

| Metric | Value |
| :--- | :--- |
| **Total Trades (T)** | 195 |
| **Win Rate** | 54.4% |
| **Total PnL** | $8,392.46 |
| **Max Drawdown** | -$6,555.32 |
| **Profit Factor** | 1.25 |
| **Median Win / Loss** | $311.69 / -$263.17 |
| **Average Win / Loss** | $399.54 / -$381.56 |
| **Average R** | 0.04R |
| **Total R** | 8.39R |

### Distribution of Exit Reasons

* **`time_exit`:** 181 Trades (92.8%)
* **`stop_loss`:** 11 Trades (5.6%)
* **`stop_loss_gap`:** 3 Trades (1.5%)

---

## 5. Key Analytical Takeaways & Future Optimization
The empirical data confirms that a genuine structural edge exists. A Profit Factor of 1.25 and a positive total return of +8.39R demonstrate an underlying short bias. However, the strategy in its current form is highly inefficient for live execution and requires refinement across three critical vectors:

* **Sample Size & Regime Limitations:** A sample of 195 trades over 6 months is statistically thin and vulnerable to regime bias. To prove structural permanence, the backtest window must be expanded to **3–5 years** to capture a multi-thousand trade sample across diverse market cycles.
* **Time-Dominant Behavior & Filtering:** Since **92.8%** of positions were liquidated via the time-based exit rule, the setup frequently traps capital in non-trending, choppy environments. Introducing structural filters—such as Relative Volume (RVOL) thresholds or separating fundamental catalysts (e.g., earnings surprises) from emotional ones (e.g., low-float retail FOMO)—is necessary to eliminate low-expectancy trades.
* **Risk Efficiency & Tighter Management:** The maximum drawdown (-$6,555.32) relative to total profit ($8,392.46) highlights substantial equity curve variance. Combined with a tight average expectancy of 0.04R, the initial risk boundaries are too wide. Tightening stop losses to structural intraday levels—such as the opening 5-minute High of Day (HOD)—and implementing conditional time-stops to exit early if a fade stalls will protect capital and scale the R:R ratio.

---

<small>*Disclaimer: This document was written by AI acting strictly as a transcription and formatting tool. All core concepts, data, quantitative analysis, parameters, and structural conclusions were provided entirely by the human author.*</small>
