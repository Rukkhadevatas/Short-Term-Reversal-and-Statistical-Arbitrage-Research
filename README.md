# Short-Term Reversal: Strategy Replication and Transaction Cost Analysis

A reproducible quantitative research project inspired by Ernest P. Chan's *Quantitative Trading*. The initial goal is to replicate a simple cross-sectional mean-reversion strategy and investigate how trading costs affect its performance. A later extension will compare short-term reversal signals with different lookback windows.

> **Status:** Work in progress. This repository documents a research replication, not a live trading system. No performance claims are made until the experiments are run and verified.

## Research questions

1. Does a one-day cross-sectional reversal signal generate positive gross returns in the chosen historical sample?
2. How sensitive are the results to transaction costs and portfolio turnover?
3. Do alternative reversal lookback windows (e.g., 5 or 20 trading days) improve out-of-sample robustness?

## Strategy definition

For asset $i$ on date $t$, let $P_{i,t}$ denote the adjusted closing price and

$$
r_{i,t} = \frac{P_{i,t}}{P_{i,t-1}} - 1.
$$

For the set $U_t$ of eligible assets with valid observations, define the equal-weighted cross-sectional mean return:

$$
\bar r_t = \frac{1}{|U_t|}\sum_{i\in U_t}r_{i,t}.
$$

The unnormalized reversal signal is $s_{i,t}=-(r_{i,t}-\bar r_t)$. The target portfolio weights are

$$
w_{i,t}=\begin{cases}
\displaystyle\frac{s_{i,t}}{\sum_{j\in U_t}|s_{j,t}|}, & \sum_{j\in U_t}|s_{j,t}|>0,\\
0, & \text{otherwise}.
\end{cases}
$$

For a complete cross-section with nonzero dispersion, the weights satisfy $\sum_i w_{i,t}=0$ and $\sum_i|w_{i,t}|=1$. Positive weights represent long positions and negative weights represent short positions.

Under a simplified convention in which the signal formed at the close of day $t-1$ determines exposure over day $t$, gross strategy return is

$$
R^{\mathrm{gross}}_t=\sum_i w_{i,t-1}r_{i,t}.
$$

A basic **approximate** transaction-cost model is

$$
\mathrm{Cost}_t=c\sum_i|w_{i,t-1}-w_{i,t-2}|,
\qquad
R^{\mathrm{net}}_t=R^{\mathrm{gross}}_t-\mathrm{Cost}_t,
$$

where $c$ is a one-way cost per unit of traded notional (e.g., $c=0.0005$ means 5 basis points). This target-weight difference is a simplified turnover proxy: an execution-aware backtest must account for weight drift, the timing of orders and fills, initial positions, and financing/shorting costs. Using a closing-price signal with execution at that same close is not generally achievable; the timing convention will be stated explicitly in the experiments.

## Planned experiments

- **01 — Chan replication:** inspect the input data, construct daily reversal weights, and compare gross and net returns.
- **02 — Reversal factors:** test prespecified 1-, 5-, and 20-day lookback signals and long–short portfolio sorting.
- **03 — Robustness:** compare transaction-cost assumptions, evaluate turnover, and perform chronological out-of-sample tests.

Planned evaluation metrics include annualized Sharpe ratio, cumulative return, maximum drawdown, turnover, and (for the factor extension) cross-sectional information coefficient (IC). Report actual sample dates, conventions, and results once available.

## Repository layout

```text
short-term-reversal-research/
├── README.md
├── requirements.txt
├── data/                       # data files (only if redistribution is permitted)
├── notebooks/
│   ├── 01_chan_replication.ipynb
│   ├── 02_reversal_factor.ipynb
│   └── 03_robustness.ipynb
├── src/                        # reusable data, signal, backtest, metric functions
├── tests/                      # checks for time alignment and portfolio accounting
└── results/                    # verified plots and summary tables
```

*The directories and notebooks above are the intended structure; create them as the work progresses.*

## Getting started

Python 3.10+ is recommended.

```bash
python -m venv .venv
```

Activate the environment:

```bash
# macOS / Linux
source .venv/bin/activate

# Windows PowerShell
.venv\Scripts\Activate.ps1
```

Install dependencies:

```bash
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

Launch Jupyter after the first notebook has been added:

```bash
jupyter lab
```

Open `notebooks/01_chan_replication.ipynb` and run its cells in order. **The notebook is planned, not included in this starter repository.**

## Data and reproducibility

The original example refers to `SPX_20071123.txt`. It is **not included here**. Obtain any book-supplied data from an authorized source and check redistribution rights before committing it to GitHub. If substituting another dataset, document its provider, price adjustments, date range, stock universe, and missing-data policy; do not expect to reproduce the book's numerical Sharpe ratios exactly.

For stock-level backtests, watch for survivorship bias, delistings, corporate actions, and changes in index constituents. A historical index-constituent universe is preferable to using today's constituents for past dates. Keep a separate untouched test period when comparing candidate signals.

## Results

**Pending replication.** Add verified figures and a compact results table here once the analysis is complete. Do not treat published or illustrative performance numbers as reproduced results.

## Limitations

This is an educational research project. A simple close-to-close backtest does not model realistic order execution, spread, market impact, borrow availability or fees, financing, or all corporate actions. Report such assumptions clearly before interpreting net returns as tradable performance.

## References

- Chan, Ernest P. *Quantitative Trading: How to Build Your Own Algorithmic Trading Business*. Wiley, 2009. (Strategy inspiration; credit the original example.)
- Additional data-provider documentation and research papers will be listed as they are used.
