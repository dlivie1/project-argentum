# Research methods

Backtests lie easily. Most of this project's process exists to stop me from fooling myself.

## The rules every strategy follows

- **Pre-register.** Rules, parameters, pass/fail gates and the data periods are written down **before** any test runs. Changing them afterwards means a new strategy with a new name.
- **Hold data back.** The most recent ~30% of history is a holdout, evaluated **once** per final candidate. Every access is logged.
- **Count every try.** Every variant ever run goes into a variant log, so a good result can be judged against how many attempts it took.
- **Gate on more than returns.** D1's gates compared it with a 50/50 buy-and-hold benchmark on:
  - maximum drawdown;
  - drawdown-adjusted return (Calmar);
  - Sharpe ratio;
  - robustness across each lookback and coin on its own;
  - a block-bootstrap test that its drawdown beats the benchmark's;
  - costs as a share of gross profit.
- **Never tune to pass.** A failed gate is a valid result. D1 failed at least one gate and was not tuned.
- **Forward test as the arbiter:**
  - frozen expectation bands, rated GREEN, YELLOW or RED;
  - a daily check that paper decisions match the backtest;
  - a cost-stress check with fees and slippage raised.

## The statistics standard (for the ETF track)

Before researching a second strategy track, I built a written methods standard from 26 sources. They range from general best practice (the American Statistical Association's statements, *Ten Simple Rules*) to finance-specific methods.

### Ground truths it's built on

These are rough figures for about 10 years of daily crypto data:
- **A Sharpe ratio of 1.0** has a 95% interval of roughly **0.4 to 1.6**.
- **Confirming a Sharpe of 1.0** takes about **3 years** of track record with crypto-like fat tails; a Sharpe of 0.5 takes about **11**. Months of paper trading can catch bugs, not prove an edge.
- **A new signal** needs roughly t ≥ 3 against its benchmark, about an information ratio of 0.95 over 10 years.

### The rules, in short

| Area | Rules |
|---|---|
| **Process** | Label every analysis exploratory or confirmatory; only confirmatory, pre-registered tests count. Define the smallest edge worth trading *before* testing. Report everything, including failures. Implementation questions ("does this still work through an ETF?") and discovery claims ("this new signal works") get different tests. |
| **Reporting** | Estimates with intervals, never "statistically significant." P-values as exact numbers only. Check every assumption; treat a surprisingly good result as a possible bug first. |
| **Forecasts** | Predict probabilities, not up/down labels. Score them with proper scoring rules (Brier, log score, ranked probability score) and calibration checks, never hit rate alone. A better forecast counts only if it also earns more after costs. |
| **Validation** | Walk-forward evaluation with purging and embargo periods sized to the label horizon and feature lookbacks. No shuffled k-fold on time series. Stationary bootstrap with an automatic block length, plus sub-period and regime breakdowns. |
| **Selection bias** | Log every trial with its returns. Gates include the Deflated Sharpe Ratio and Hansen's Superior Predictive Ability test against every benchmark, with Harvey–Liu haircut Sharpe ratios and the probability of backtest overfitting reported too. |
| **Data** | Raw data untouched, every step scripted, ISO timestamps in UTC, gaps flagged and never filled. A data dictionary records when each value becomes known, which guards against look-ahead bias. |

### Main sources

- **Sharpe ratios and overfitting:**
  - Lo (2002), *The Statistics of Sharpe Ratios*, Financial Analysts Journal.
  - Bailey & López de Prado (2014), *The Deflated Sharpe Ratio*, Journal of Portfolio Management.
  - Bailey, Borwein, López de Prado & Zhu (2017), *The Probability of Backtest Overfitting*, Journal of Computational Finance.
- **Data snooping and multiple testing:**
  - White (2000), *A Reality Check for Data Snooping*, Econometrica.
  - Hansen (2005), *A Test for Superior Predictive Ability*, Journal of Business & Economic Statistics.
  - Harvey & Liu (2015), *Backtesting*, Journal of Portfolio Management.
  - Harvey, Liu & Zhu (2016), *…and the Cross-Section of Expected Returns*, Review of Financial Studies.
- **Forecast evaluation:**
  - Gneiting & Raftery (2007), *Strictly Proper Scoring Rules*, Journal of the American Statistical Association.
  - Gneiting, Balabdaoui & Raftery (2007), *Probabilistic forecasts, calibration and sharpness*, Journal of the Royal Statistical Society B.
  - Diebold & Mariano (1995) and Diebold (2015), on comparing predictive accuracy.
- **Time-series validation:**
  - Politis & Romano (1994), *The Stationary Bootstrap*, plus Politis & White (2004).
  - Hyndman & Athanasopoulos, *Forecasting: Principles and Practice*.
  - Bergmeir, Hyndman & Koo (2018), on cross-validation for time series.
  - López de Prado (2018), *The 10 Reasons Most Machine Learning Funds Fail*.
- **General practice:**
  - Wasserstein & Lazar (2016) and Wasserstein, Schirm & Lazar (2019), on p-values.
  - Greenland et al. (2016), a guide to misinterpretations of statistical tests.
  - Kass et al. (2016), *Ten Simple Rules for Effective Statistical Practice*.
  - van Smeden (2022), a short list of common research pitfalls.
  - The ASA's *Ethical Guidelines for Statistical Practice* (2022).

## A note on honesty

The most useful thing these sources taught me: set the bar before you look, and expect most ideas to fail it. A research process that rarely says "no" isn't finding edges; it's finding noise.
