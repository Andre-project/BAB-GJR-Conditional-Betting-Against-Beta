# BAB-GJR — Conditional Betting Against Beta

> Replication and extension of **Frazzini & Pedersen (2014)** — replacing 
> rolling OLS betas with GJR-GARCH(1,1) conditional betas for the 
> Betting Against Beta factor.

---

## Motivation

The standard BAB factor uses rolling OLS betas — 252-day vol windows, 
1260-day correlation windows. It works, but it's slow. When volatility 
spikes (GFC, COVID), OLS betas take months to catch up.

Two problems with OLS in volatile regimes:
1. **Too slow** — long lookback windows mean beta estimates lag reality
2. **Symmetric** — OLS treats positive and negative shocks identically, 
   which doesn't reflect how equity vol actually behaves

This project replaces OLS with a conditional estimator:
- **GJR-GARCH(1,1)** for market variance — captures the leverage effect 
  (negative shocks → more vol than equivalent positive shocks)
- **EWMA covariance** (λ=0.94, ~11-day half-life) — reacts to new 
  information in days, not months

GARCH parameters are estimated once on in-sample data (2000–2015) and 
frozen. No re-estimation out-of-sample, no data snooping.

---

## Results

**Out-of-sample period: 2016–2022** (84 months, post-publication window)

| Strategy | Ann. Return | Ann. Vol | Sharpe | Max DD |
|----------|------------|----------|--------|--------|
| BAB-OLS  | 1.05%      | 9.00%    | 0.12   | -26.2% |
| BAB-GJR  | 9.20%      | 16.90%   | 0.54   | -14.6% |

OLS barely breaks even post-2016 — consistent with the well-documented 
decay of the BAB premium after publication. GJR recovers most of it.

**Caveats worth knowing:**
- GJR turnover is ~1900% annualised (vs ~278% for OLS) — the fast signal 
  comes with a rebalancing cost (~90bps/year at 5bps/trade)
- Return kurtosis is high (47 vs 1.45) — a few outlier months do a lot 
  of work
- Jobson-Korkie test: p=0.40 — the Sharpe difference is economically 
  large but 84 months isn't enough to confirm it statistically


---

## Data

This project uses **CRSP daily returns** via WRDS (institutional access 
required). The notebook includes a dedicated data loading section — 
adapt it to your own source or substitute with any daily returns dataset 
covering NYSE/AMEX/NASDAQ common shares.

Fama-French factors are pulled automatically via `pandas-datareader` 
from Kenneth French's data library.

---

## Getting Started

```bash
git clone https://github.com/your-username/BAB-GJR.git
cd BAB-GJR
pip install -r requirements.txt
jupyter notebook notebooks/bab_gjr.ipynb
```

---

## Stack

| Library | Use |
|---------|-----|
| `arch` | GJR-GARCH(1,1) estimation |
| `pandas` / `numpy` | data wrangling |
| `statsmodels` | Newey-West regressions |
| `pandas-datareader` | Fama-French factors |
| `matplotlib` / `seaborn` | plots |

---

## References

- Frazzini, A. & Pedersen, L.H. (2014). *Betting Against Beta*. Journal of Financial Economics.
- Glosten, L.R., Jagannathan, R. & Runkle, D.E. (1993). *On the Relation between the Expected Value and the Volatility of the Nominal Excess Return on Stocks*. Journal of Finance.
- Bollerslev, T. (1986). *Generalized Autoregressive Conditional Heteroskedasticity*. Journal of Econometrics.

---

## Authors

Built as part of a quantitative asset management course at **Católica Lisbon School of Business & Economics** (2026).

[Andre Lopez](https://github.com/) · [Alice Dubuis](https://github.com/) · [Fabián Reyes Díaz](https://github.com/)

---

*Contributions, issues, and forks welcome.*
