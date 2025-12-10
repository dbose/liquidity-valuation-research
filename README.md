# 📘 Liquidity Regimes & The Death (and Return) of Valuations

### *A Macro–Factor Research Framework using Penalized Liquidity Indices & Regime-Switching Models*

This repository contains a quantitative research framework that investigates how **monetary liquidity regimes** drive equity valuations, factor returns, and market dynamics.
The project blends **macro-finance**, **econometrics**, and **machine-learning techniques** (Sparse PCA, HMM) to construct a structural model of liquidity-driven markets.

> **Core Thesis:**
> Traditional valuation metrics (like CAPE) appear “dead” only during periods of abundant liquidity.
> **Valuation predictability is conditional on liquidity regimes**, not obsolete.

---

## Motivation

Since 2008, markets have been shaped by:

* Quantitative Easing (QE1–QE4)
* ZIRP/NIRP monetary policy
* Fed balance sheet expansion
* Rapid M2 growth relative to GDP
* Long-term suppression of real interest rates

This raises two key questions:

1. **How much of equity pricing is driven by liquidity vs fundamentals?**
2. **When do valuations matter again?**

This project provides a **systematic answer** using a data-driven liquidity index and regime-switching models.

---

## Research Architecture

### **1. Macro Liquidity Data Pipeline (FRED API)**

Monthly FRED series:

* M2SL (M2 Money Stock)
* WALCL (Fed Balance Sheet)
* TB3MS, DGS10 (Term Structure)
* AAA, BAA (Corporate Yields)
* CPI
* GDP (quarterly → resampled to monthly)
* Optional: ON/RRP, TGA, Reserve Balances

Includes robust alignment, cleaning, and interpolation rules.

---

### **2. Augmented Liquidity Proxy Matrix**

Constructs a structural proxy set:

| Category              | Variables                                      |
| --------------------- | ---------------------------------------------- |
| **Flows**             | Δlog(M2), Δlog(FED_BAL)                        |
| **Prices**            | Credit spread, Term spread, Real rate          |
| **Stocks**            | Trend residuals (excess M2, excess Fed assets) |
| **Macro adjustment**  | Liquidity vs GDP growth (3-year window)        |
| **Policy indicators** | ZIRP dummy                                     |

All proxies are **sign-flipped** such that **higher = easier liquidity**.

---

### **3. Penalized Liquidity Index L(t) (Sparse PCA)**

A sparse PCA / elastic-net PCA that:

* Produces interpretable macro loadings
* Reduces multicollinearity
* Extracts the dominant liquidity factor

$$
L(t) = w^\top Z_t
$$

Where ( Z_t ) is the standardized, sign-adjusted proxy matrix.

---

### **4. Regime Detection via Gaussian HMM**

A Hidden Markov Model fit to ( L(t) ):

* Learns **latent liquidity regimes**
* Provides smoothed and filtered probabilities
* Outputs canonical regimes:

| State | Label       | Interpretation                      |
| ----- | ----------- | ----------------------------------- |
| 0     | **Tight**   | Funding stress, QT, high real rates |
| 1     | **Neutral** | Transitional environments           |
| 2     | **High**    | QE, ZIRP, liquidity surges          |

---

### **5. Valuation Spread Construction (CAPE Baseline)**

Instead of rolling z-scores, we anchor valuation to a **historical CAPE baseline (≈16.04)**:

$$
V_{\text{spread}}(t) = \frac{16.04 - \text{CAPE}_t}{16.04}
$$

Higher values ⇒ **cheaper relative to long-term history**.

This avoids look-ahead bias and matches economic intuition.

---

### **6. Regime-Conditioned Factor Analysis**

We merge:

* Fama–French factor returns
* The liquidity index
* HMM-inferred regimes
* CAPE-based valuation cheapness

Then estimate:

* Regime Sharpe ratios
* Predictive regressions
* Cross-sectional spreads
* Sign flips of SMB, HML, CMA across liquidity states

---

### **7. Visualization Layer**

Publication-quality plots:

* L(t) with regime shading
* S&P 500 vs liquidity regimes
* Valuation Spread vs regimes
* Factor returns conditioned on liquidity states

---

## Key Findings

1. **Valuation predictability is regime-dependent**
   Predictive power is strong only under *tight liquidity*.

2. **Factor premia flip sign with liquidity**
   Example:

   * SMB positive in Tight regimes
   * SMB negative in High-liquidity regimes

3. **Liquidity explains long stretches of overvaluation**
   The “death of value” is not structural — it is a *liquidity artifact*.

4. **Liquidity ≈ the hidden state of the equity market**
   HMM states map cleanly to major policy eras: GFC, ZIRP, QE waves, COVID, QT.

---

## Repository Target Structure

```bash
.
├── data/
│   ├── fred_raw/              # Raw FRED series
│   ├── processed/             # Cleaned monthly macro data
│   └── valuations/            # CAPE, PB, etc.
├── notebooks/
│   ├── 01_liquidity_data.ipynb
│   ├── 02_sparse_pca_L.ipynb
│   ├── 03_hmm_regimes.ipynb
│   ├── 04_valuation_spread.ipynb
│   ├── 05_factor_regressions.ipynb
│   └── 06_visualizations.ipynb
├── src/
│   ├── data_loader.py
│   ├── liquidity_proxies.py
│   ├── sparse_pca_model.py
│   ├── hmm_model.py
│   ├── valuation.py
│   ├── factor_analysis.py
│   └── plot_utils.py
└── paper/
    └── Liquidity_Regimes_Valuations.pdf
```

---

## Example: Build the Liquidity Index

```python
from src.liquidity_proxies import build_liquidity_proxies_augmented
from src.sparse_pca_model import fit_sparse_pca
from src.hmm_model import fit_hmm_on_liquidity

macro_df = load_fred_data()
proxies = build_liquidity_proxies_augmented(macro_df)
L_t, pca_model = fit_sparse_pca(proxies)
hmm, regimes_df = fit_hmm_on_liquidity(L_t)
```

---

## Example: Plot L(t), S&P 500, and Valuation Spread

```python
from src.plot_utils import plot_liquidity_equity_valuation

plot_liquidity_equity_valuation(L_t, spx_m, V_spread_t, regimes_df)
```

---

## Impact & Use Cases

This research framework demonstrates:

* Advanced econometric modeling
* Factor-based portfolio research
* Regime-switching inference
* Macro–quant integration
* Machine learning techniques applied to financial economics

Perfect for:

* Quant Research roles
* Macro Strategy
* Systematic Investing
* Data Science in Finance
* Academic publication foundations

---

## Future Enhancements

* Joint HMM on valuations + liquidity
* Robust PCA (Huber / LAD PCA)
* Regime-switching VAR on macro variables
* Cross-country comparison (EU, Japan, EM)
* Liquidity-adjusted expected return model

---

## Citation

```
Bose, Debasish (2025). Liquidity Regimes and the Death (and Return) of Valuations.
A Quantitative Macro–Factor Research Framework.
```

---

If you'd like, I can also generate:

* A GitHub banner image
* Badges (Python version, license, build status)
* A one-page abstract
* A project logo for the repo
* Example results charts to embed in README

Just tell me.
