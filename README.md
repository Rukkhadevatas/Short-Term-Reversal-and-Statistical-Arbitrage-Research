# Short-Term Reversal: Mean-Reversion Backtests on U.S. Sector ETFs

A small, reproducible quantitative research project inspired by Ernest P. Chan's *Quantitative Trading*. Using Yahoo Finance adjusted daily prices for nine U.S. sector ETFs (2018–2025), this project evaluates a one-day cross-sectional mean-reversion rule and extends the analysis to **1-, 5-, and 20-day reversal factors**.

**Main finding:** The four tested variants had gross annualized Sharpe ratios near zero or slightly negative, while a 5-bp one-way cost assumption reduced annualized Sharpe by **0.51–2.25 points**.

## Results

All Sharpe ratios below are annualized with daily strategy returns. Net performance includes an assumed **5 bps (0.05%) one-way transaction cost** per unit of traded notional. Values are from the user's completed notebook runs.

| Strategy | Gross annualized Sharpe | Net annualized Sharpe | Sharpe decline (gross − net) |
|:--|--:|--:|--:|
| Chan's one-day cross-sectional mean reversion | −0.1300 | −2.1130 | 1.9830 |
| 1-day reversal factor (3 long / 3 short) | −0.1614 | −2.4108 | 2.2494 |
| 5-day reversal factor (3 long / 3 short) | +0.0442 | −0.9068 | 0.9510 |
| 20-day reversal factor (3 long / 3 short) | −0.0185 | −0.5248 | 0.5063 |

**Remark:** The Chan's and 1-day factor rows are intentionally distinct: both use one-day returns to form a signal, but Chan's assigns continuous cross-sectional weights while the factor portfolio holds equally weighted top/bottom groups. Thus their results need not match.

**Interpretation:** Gross performance was close to zero across the four variants; modeled trading costs produced much weaker net results. The shorter-horizon approaches experienced the largest Sharpe declines under the reported backtests. This is a descriptive result for nine U.S. sector ETFs during 2018–2025, not evidence that reversal is universally ineffective. 

## Data

- **Provider:** Yahoo Finance, downloaded through [`yfinance`](https://github.com/ranaroussi/yfinance)
- **Universe:** XLB, XLE, XLF, XLI, XLK, XLP, XLU, XLV, XLY (nine U.S. sector ETFs)
- **Sample:** January 2018–December 2025; requested with `start="2018-01-01"`, `end="2026-01-01"` (exclusive end date)
- **Frequency:** Daily
- **Prices:** Auto-adjusted close (`auto_adjust=True`)
- **Missing data:** Only dates with prices for all nine ETFs are retained

The ETF universe is fixed in this study. Data are downloaded when each notebook runs; no proprietary source data are included.

## Methodology

### 1. Chan's one-day cross-sectional mean reversion

For ETF $i$ on trading day $t$, define the daily return and the cross-sectional average:

$$
r_{i,t}=\frac{P_{i,t}}{P_{i,t-1}}-1, \qquad
\bar r_t=\frac{1}{N}\sum_{j=1}^{N}r_{j,t}.
$$

The target weights are

$$
w_{i,t}=-\frac{r_{i,t}-\bar r_t}{\sum_{j=1}^{N}|r_{j,t}-\bar r_t|},
$$

with zero weights if the denominator vanishes. These weights are dollar-neutral and have unit gross exposure when the signal is nonzero. The strategy buys relative losers and shorts relative winners. The notebook computes gross and net returns and reports annualized Sharpe and other performance statistics.

### 2. Reversal factors

For lookback $k\in\{1,5,20\}$ trading days:

$$
F_{i,t}^{(k)}=-\left(\frac{P_{i,t}}{P_{i,t-k}}-1\right).
$$

Each day, ETFs are ranked by the factor. The backtest buys the three highest-ranked ETFs (recent losers) and shorts the three lowest-ranked ETFs (recent winners), with equal positions and gross exposure of 1 (long +0.5, short -0.5). The notebook also computes daily **Spearman rank IC** by correlating ranked signals with ranked target returns. The IC is an analysis output, not an independently verified performance claim in the results table above.

### 3. Backtest assumptions

- **Signal and execution:** Signals are formed after day $t$ closes; the model assumes a trade at the *next close*, followed by the next close-to-close return (a two-row lag in the daily return table).
- **Transaction cost:** A flat **5 basis points (0.05%) one-way** charge per unit of traded notional.
- **Turnover:** Computed from the required rebalance relative to prior positions after their weights have drifted with returns.
- **Evaluation:** Both notebooks calculate **annualized Sharpe**; the second notebook also plots gross and net cumulative wealth.

## Scope and limitations

- The ETF portfolios differ from Chan's original S&P 500 constituent-stock example; this is a small strategy adaptation, not only a numerical reproduction of the book's reported Sharpe ratio.
- The study only evaluates nine sector ETFs, not a broad, historically reconstructed equity universe.
- The reported results are in-sample descriptive backtests; no independent out-of-sample test or multiple-testing adjustment has been completed.
- Transaction costs are stylized and may understate or overstate realizable execution costs, it does not fully model bid–ask spreads, borrow fees, financing, market impact, order execution, or tradability at the assumed close.

## Reference

Chan, Ernest P. *Quantitative Trading: How to Build Your Own Algorithmic Trading Business*. Wiley, 2009.

Data: [Yahoo Finance](https://finance.yahoo.com/) 
