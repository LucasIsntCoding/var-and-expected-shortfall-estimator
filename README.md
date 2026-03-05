# Portfolio VaR & Expected Shortfall (CVaR) Engine

A production-style Python risk analytics module for estimating **1-day portfolio Value at Risk (VaR)** and **Expected Shortfall (ES/CVaR)** using both **historical simulation** and **Gaussian (parametric)** methods, with **rolling out-of-sample forecasting** and formal **VaR backtesting** (Kupiec + Christoffersen tests).

> VaR/ES are reported as **positive loss magnitudes** (e.g., `0.021` = **2.1%** one-day loss).

---

## Key Features

- **Portfolio return construction**
  - Multi-asset prices → log or arithmetic returns
  - Portfolio aggregation via constant-mix (**daily rebalancing**) or **buy-and-hold** NAV

- **Risk estimation**
  - **Historical VaR/ES** (empirical quantiles + tail mean)
  - **Gaussian VaR/ES** (mean–variance + closed-form ES)

- **Rolling risk forecasts (out-of-sample)**
  - Sliding window estimation (e.g., 250 trading days)
  - One-step-ahead VaR/ES time series for 95% and 99%

- **Backtesting and diagnostics**
  - Exception indicator: `1{ loss_t > VaR_t }`
  - **Kupiec Unconditional Coverage (UC)** test
  - **Christoffersen Independence (IND)** test
  - **Christoffersen Conditional Coverage (CC)** test (UC + IND)

- **Visualisation**
  - Return distribution with VaR thresholds
  - Loss time series vs forecast VaR with exceedance markers

---

## Methodology (Technical Summary)

### Portfolio Loss
Let \( r_t \) be the portfolio return. Define loss as:
\[
L_t = -r_t
\]

### Historical Simulation
- VaR at confidence \( \alpha \) is the empirical quantile of losses:
\[
\mathrm{VaR}_\alpha = Q_\alpha(L)
\]
- ES is the mean loss beyond VaR:
\[
\mathrm{ES}_\alpha = \mathbb{E}[L \mid L \ge \mathrm{VaR}_\alpha]
\]

### Gaussian (Parametric) Model
Assume returns \( R \sim \mathcal{N}(\mu,\sigma^2) \).
Let \( p = 1-\alpha \) and \( z_p = \Phi^{-1}(p) \).

- VaR (expressed as positive loss):
\[
\mathrm{VaR}_\alpha = -\left(\mu + \sigma z_p\right)
\]
- ES (lower-tail) using the normal pdf \( \phi \):
\[
\mathrm{ES}_\alpha = -\left(\mu - \sigma \frac{\phi(z_p)}{p}\right)
\]

### Backtesting
Exceptions:
\[
I_t = \mathbf{1}\{L_t > \mathrm{VaR}_{\alpha,t}\}
\]
Expected exception rate: \( p = 1-\alpha \)

Implemented tests:
- **Kupiec (UC)**: checks if observed exception frequency matches \( p \)
- **Christoffersen (IND)**: checks exception independence (Markov vs iid)
- **Christoffersen (CC)**: joint UC + IND

---

## Repository Structure (Recommended)

You can keep this as a single file, or structure it like:
