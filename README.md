
# gap-up-short-backtester

An event-driven quantitative backtesting framework in Python designed to evaluate and optimize delayed gap-up short strategies on equities using Interactive Brokers historical data.

## Systematic Backtesting: Shorting Extreme Overnight Gap-Ups

1. Methodology & Data Collection
The core objective of this backtest was to determine whether extreme overnight gap-ups serve as a statistically viable short setup within the recent market regime. To isolate a potential edge, the strategy was evaluated against a 2-year historical data horizon encompassing the entire universe of US equities listed on the NASDAQ and NYSE, rather than a restricted set of pre-selected tickers. This comprehensive approach ensured that any high-momentum catalyst—where a stock opened significantly higher than its prior day's close—was captured, eliminating selection bias and providing a robust statistical baseline for the setup.

The infrastructure was built using a three-step pipeline:
* **Data Ingestion:** Automated extraction of daily historical screener data directly from FinViz via a Python script to capture all equities hitting the target gap threshold.
* **Storage:** The raw scraped data is loaded directly into a pandas DataFrame in memory for immediate filtering and manipulation.
* **Historical Sourcing:** Programmatic connection to the Interactive Brokers API to retrieve historical price data for each screened ticker.

### 2. Exploratory Data Analysis (EDA)
Correlation and regression analyses were conducted across a wide array of features to determine if specific variables could reliably predict intraday price action (execution returns).

The following metrics were cross-examined:
* **Gap Characteristics & Technicals:** Gap vs. open-to-close, open-to-high, and open-to-low; gap date vs. open-to-close; z-score gap and Rate of Change (ROC) vs. open-to-close.
* **Market Structure & Fundamentals:** Float size vs. open-to-close/open-to-high; short interest vs. open-to-high/open-to-low; market cap, prior performance, and sector vs. open-to-close.
* **Temporal Dynamics:** Cumulative return tracking across various holding periods to isolate the optimal exit window.

> **Conclusion:** While macroscopic linear correlations failed to yield a simple entry edge across individual variables, a distinct negative drift was observed over extended holding periods. This structural tendency to fade confirmed the underlying short bias, shifting the focus toward optimizing the exact exit horizon.

### 3. Parameter Tuning & Optimization Framework
Leveraging the structural negative drift identified during the EDA phase, the approach shifted to systematic empirical optimization. Using a dedicated training subset, multi-dimensional bucket tests were executed across a wide spectrum of proprietary **Stop Loss (SL)** and **Take Profit (TP)** brackets, running parallel to variations in optimized holding periods to exploit the timing of the decay.

To avoid curve-fitting, parameter permutations were evaluated against a comprehensive risk-reward matrix tracking:
* Win Rate & Median Risk-to-Reward (R:R) for mathematical expectancy.
* Maximum Drawdown & Max Risk to assess structural pain tolerance.
* Realistic, per-trade transaction fees to account for execution friction and slippage.

To avoid curve-fitting, parameter permutations were evaluated against a comprehensive risk-reward matrix tracking:
* Win Rate & Median Risk-to-Reward (R:R) for mathematical expectancy.
* Maximum Drawdown & Max Risk to assess structural pain tolerance.
* Realistic, per-trade transaction fees to account for execution friction and slippage.

### 4. Empirical Results & Performance Dashboard

#### Core Performance Metrics
| Metric | Value |
| :--- | :--- |
| **Total Trades (T)** | 186 |
| **Win Rate** | 54.3% |
| **Total PnL** | $3,117.61 |
| **Max Drawdown** | -$9,817.07 |
| **Profit Factor** | 1.08 |
| **Median Win** | $320.55 |
| **Median Loss** | -$316.08 |
| **Avg Win** | $426.26 |
| **Avg Loss** | -$469.81 |
| **Avg R** | 0.02R |
| **Total R** | 3.12R |

#### Distribution of Exit Reasons
| Exit Reason | Trades | Percentage |
| :--- | :--- | :--- |
| **time_exit** | 167 | 89.8% |
| **stop_loss** | 11 | 5.9% |
| **stop_loss_gap** | 5 | 2.7% |
| **take_profit** | 2 | 1.1% |
| **take_profit_gap** | 1 | 0.5% |

### 5. Key Analytical Takeaways & Future Optimization
The empirical data confirms that a genuine structural edge exists. A Profit Factor of 1.08 and a positive total return of +3.12R demonstrate an underlying short bias. However, the strategy in its current form is highly inefficient for live execution and requires refinement across three critical vectors:

* **Sample Size & Regime Limitations:** A sample of 186 trades over 6 months is statistically thin and vulnerable to regime bias.
* **Time-Dominant Behavior & Filtering:** Since 89.8% of positions were liquidated via the time-based exit rule, the setup frequently traps capital in non-trending, choppy environments. Introducing structural filters—such as Relative Volume (RVOL) thresholds or separating fundamental catalysts (e.g., earnings surprises) from emotional ones (e.g., low-float retail FOMO)—is necessary to eliminate low-expectancy trades.
* **Risk Efficiency & Tighter Management:** The maximum drawdown (-$9,817.07) relative to total profit ($3,117.61) highlights substantial equity curve variance. Combined with a tight average expectancy of 0.02R, the initial risk boundaries are too wide. Tightening stop losses to structural intraday levels—such as the opening 5-minute High of Day (HOD)—and implementing conditional time-stops to exit early if a fade stalls will protect capital and scale the R:R ratio.

***

---

## Update: Version 2 Backtest Results (Stalking Window vs. Increased Gap Threshold)

* A second iteration (Version 2) of the backtester was deployed to evaluate a more aggressive macro setup: **adjusting the initial gap threshold upward** to isolate more extreme overnight extensions, paired with a defined **stalking window for entries** and modified holding periods.
* Separate, isolated testing confirmed that an increased gap threshold shows strong standalone promise and upside potential. However, when combined with the stalking window—designed to delay execution and filter entry based on immediate post-open structural boundaries—the results deteriorated heavily.
* The empirical data indicates that while the higher gap criteria is structurally viable, the filtering constraints of the stalking window introduced severe execution drag, effectively neutralizing the core edge.

### Performance Summary

#### Core Performance Metrics
| Metric | Value |
| :--- | :--- |
| **Total Trades (T)** | 186 |
| **Win Rate** | 56.5% |
| **Total PnL** | $-526.21 |
| **Max Drawdown** |  $-10,312.10 |
| **Profit Factor** | 0.99 |
| **Median Win** | $256.81 |
| **Median Loss** | -$270.35 |
| **Avg Win** |  $329.94 |
| **Avg Loss** | -$434.20 |
| **Avg R** | 0.00R |
| **Total R** | -0.53R |

#### Distribution of Exit Reasons
| Exit Reason | Trades | Percentage |
| :--- | :--- | :--- |
| **time_exit** | 171 | 91.9% |
| **stop_loss** | 8 | 4.3% |
| **stop_loss_gap** | 6 | 3.2% |
| **take_profit** | 1 | 0.5% |
| **take_profit_gap** | 0 | 0.5% |

*Disclaimer: This document was written by AI acting strictly as a transcription and formatting tool. All core concepts, data, quantitative analysis, parameters, and structural conclusions were provided entirely by the human author.
