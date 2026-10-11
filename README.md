# Systematic Futures Trading Model

Real-time execution telemetry, automated post-trade accounting, and risk analytics for a systematic perpetual futures strategy deployed on Bitunix.

📊 **Live Performance Dashboard:** [Bekijk Live Dashboard](https://thijs-guillen-verheijen.github.io/systematic-trading-model/)

---

## Architecture Overview

The system isolates execution and risk boundaries from raw signal generation, running 24/7 on a headless Linux VPS via dedicated `systemd` daemon services:

* **Execution Engine (Proprietary):** Submits volatility-aware orders via authenticated HMAC-SHA256 REST endpoints.
* **Accounting & Telemetry:** Monitors order lifecycle states, fills, and realized PnL in real time.
* **Audit Pipeline:** Automatically aggregates equity progression, calculates drawdown metrics, and compiles an interactive analytics interface.

---

## Risk Allocation & Dynamic Position Sizing

Position sizing is computed dynamically upon each validated entry condition, strictly avoiding static contract lots.

### 1. Risk Budgeting
Given account equity $C$ and an asset-specific risk budget parameter $\alpha$:

* **ETH:** $\alpha = 0.005$ (0.5% equity at risk)
* **SOL:** $\alpha = 0.010$ (1.0% equity at risk)

$$\text{Max Risk} = C \cdot \alpha$$

### 2. Order Sizing Formulation
For a designated entry price $P_{\text{entry}}$ and stop-loss threshold $P_{\text{sl}}$, the stop distance is defined as:

$$\Delta P = \vert{}P_{\text{entry}} - P_{\text{sl}}\vert{}$$

The executed contract quantity $Q$ is constrained such that an adverse exit limits capital depletion strictly to the risk budget:

$$Q = \frac{C \cdot \alpha}{\Delta P}$$

Subject to hard leverage boundaries:

$$Q \cdot P_{\text{entry}} \le 3 \cdot C$$

Profit targets maintain a constant risk-to-reward ratio of $1 : 2.5$ relative to $\Delta P$.

---

## Out-of-Sample Performance Audit

All trade records are audited sequentially upon execution completion:

* **Telemetry Log:** Recorded with microsecond timestamps, entry/exit prices, fees, and realized PnL in `trades.csv`.
* **Telemetry Interface:** Interactive Plotly-rendered dashboard displaying High Water Mark (HWM) trajectories, episode-based drawdowns, and distribution statistics.
