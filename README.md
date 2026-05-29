# Time-Varying Sector Beta Analysis Around Market Stress Events

## Project Overview

This project analyzes how the market sensitivity (beta) of major U.S. sector ETFs changes over time, with particular focus on selected market stress and macro regime periods between 2018 and 2024. Rather than treating beta as a static parameter, the analysis estimates rolling betas for each sector and compares their behavior across distinct events: the COVID crash, the 2022 Fed rate-hike cycle, the SVB banking crisis, and the AI-driven technology rally.

The output is a set of visual diagnostics and summary statistics that characterize how sector-level systematic risk shifts across different market environments.

---

## Research Question

Are sector ETF betas stable over time, or do they shift meaningfully during macro stress and market regime changes? If they shift, which sectors are most sensitive to regime transitions, and which maintain relatively stable exposures?

---

## Motivation

Standard CAPM assumes a constant beta. In practice, equity market sensitivity tends to change during periods of elevated volatility, macro shocks, or structural market rotation. For risk management and portfolio construction, understanding whether a sector's beta is stable or time-varying has direct implications for how dynamic hedging ratios and risk exposures should be managed.

This project treats the question empirically using publicly available ETF and macro data, without imposing a structural model.

---

## Data Sources / Inputs

**Sector ETFs (adjusted close prices, 2018–2024):**

| Ticker | Sector |
|--------|--------|
| XLK | Technology |
| XLE | Energy |
| XLF | Financials |
| XLV | Health Care |
| XLP | Consumer Staples |
| XLU | Utilities |
| XLY | Consumer Discretionary |
| SPY | S&P 500 (market benchmark) |

**Macroeconomic variables:**

- 3-month Treasury bill rate (DTB3) — used as risk-free rate proxy
- Federal Funds Rate (FEDFUNDS)
- 10-year Treasury yield (DGS10)
- CPI (CPIAUCSL)
- VIX (CBOE Volatility Index)
- NBER recession indicator / manually defined regime windows

**Event/crisis periods (defined in `data/raw/events.csv`):**

| Event | Start | End | Type |
|-------|-------|-----|------|
| COVID Crash | 2020-02-19 | 2020-03-23 | Crisis |
| COVID Recovery | 2020-03-24 | 2020-12-31 | Recovery |
| 2022 Bear Market | 2022-01-03 | 2022-10-12 | Crisis |
| Fed Rate-Hike Cycle | 2022-03-16 | 2023-07-26 | Macro |

Additional events used in the crisis comparison (SVB Crisis, AI Rally) are labeled directly in the analysis output.

---

## Methodology

1. **Data collection:** Adjusted close prices sourced via `yfinance`; macro data from FRED.
2. **Return construction:** Daily log returns and excess returns (sector return minus risk-free rate) computed for each ETF.
3. **Weekly aggregation:** A weekly return panel is constructed as a robustness check and for reduced noise in rolling estimates.
4. **Rolling beta estimation:** 52-week rolling OLS regression of sector excess returns on SPY excess returns. Each window produces one beta estimate per sector per week.

   ```
   excess_return_sector(t) = α + β · excess_return_market(t) + ε(t)
   ```

5. **Crisis-period comparison:** Mean rolling beta for each sector is computed within each defined event window and compared across periods.
6. **Robustness:** A second rolling specification using a 26-week window is included to test sensitivity to window length.

---

## Repository Structure

```
time-varying-sector-betas/
├── data/
│   ├── raw/
│   │   ├── sector_etf_adj_close.csv     # Adjusted close prices (SPY + 7 sector ETFs)
│   │   └── events.csv                   # Crisis/regime event definitions
│   └── processed/
│       ├── macro_data.csv               # Merged macro + regime labels
│       ├── returns_clean.csv            # Daily returns + excess returns panel
│       └── returns_clean_weekly.csv     # Weekly returns panel
├── figures/
│   ├── rolling_beta_linechart.png       # 52-week rolling beta over time, all sectors
│   ├── rolling_beta_heatmap.png         # Beta heatmap: sector × time
│   ├── rolling_beta_boxplot.png         # Distribution of rolling betas by sector
│   ├── rolling_beta_crisis_comparison.png  # Mean beta by sector across crisis periods
│   └── rolling_beta_robustness_26w.png  # 26-week rolling beta (robustness check)
├── outputs/
│   └── crisis_beta_summary.csv          # Tabular mean beta by event × sector
├── reports/
│   ├── final_report.pdf                 # Final written report
│   └── final_project_integrated_notebook.pdf  # PDF export of analysis notebook
├── README.md
├── requirements.txt
└── .gitignore
```

> **Note:** The executable analysis notebook (`.ipynb`) is not currently included in this repository. The `reports/` folder contains a PDF export of the original notebook. The source notebook can be added or restored from the original working file.

---

## Key Outputs

| File | Description |
|------|-------------|
| `figures/rolling_beta_linechart.png` | Time series of 52-week rolling beta for each sector ETF |
| `figures/rolling_beta_heatmap.png` | Heatmap showing beta levels across sectors and time |
| `figures/rolling_beta_boxplot.png` | Distribution of rolling betas by sector over the full sample |
| `figures/rolling_beta_crisis_comparison.png` | Bar chart comparing mean sector beta across event periods |
| `figures/rolling_beta_robustness_26w.png` | Alternative 26-week rolling beta for robustness |
| `outputs/crisis_beta_summary.csv` | Tabular summary: mean beta per sector per event period |
| `reports/final_report.pdf` | Full written report with methodology and discussion |

---

## Main Findings

The following observations are based on the rolling beta estimates and crisis comparison outputs.

**Betas are time-varying across all sectors.** No sector maintains a constant beta across the full 2018–2024 sample. Estimates shift substantially between calm and stressed periods.

**Sector rankings shift across events.** Energy (XLE) carried the highest mean beta during the COVID Crash (β ≈ 1.40), but dropped to among the lowest during the AI Rally (β ≈ 0.50). Technology (XLK) followed the opposite trajectory, compressing during the COVID Crash (β ≈ 1.09) and rising to its highest observed level during the AI Rally (β ≈ 1.40).

**Defensive sectors consistently showed lower betas.** Consumer Staples (XLP) and Utilities (XLU) had the lowest betas across nearly all event periods. During the AI Rally, both sectors reached their lowest readings in the sample (β ≈ 0.39), suggesting they did not participate in the broader market sensitivity expansion.

**Consumer Discretionary and Technology elevated during recent periods.** XLY (Consumer Discretionary) showed elevated betas during the Rate Hike cycle (β ≈ 1.31) and SVB Crisis (β ≈ 1.29), consistent with its sensitivity to interest rates and consumer credit conditions.

**The 26-week robustness specification broadly confirms the 52-week results**, with higher volatility in the estimates due to shorter averaging.

These findings are descriptive. Statistical significance of beta differences across regimes is not assessed in this version of the analysis.

---

## How to Reproduce

This repository currently includes PDF exports of the final report and analysis notebook. The executable notebook is not present in the repo.

To re-run the analysis once the notebook is restored:

```bash
# 1. Clone the repository
git clone https://github.com/<your-username>/time-varying-sector-betas.git
cd time-varying-sector-betas

# 2. Create and activate a virtual environment
python -m venv venv
source venv/bin/activate        # macOS/Linux
# venv\Scripts\activate         # Windows

# 3. Install dependencies
pip install -r requirements.txt

# 4. Launch Jupyter
jupyter notebook
# Then open the notebook from the notebooks/ directory once restored
```

**Data note:** The processed data files in `data/processed/` are included in the repo. Raw ETF price data was originally collected using `yfinance`; macro data was sourced from FRED. If re-collecting raw data, the `data/raw/sector_etf_adj_close.csv` file provides a reference for the tickers and date range used.

---

## Requirements

```
pandas
numpy
matplotlib
seaborn
scipy
statsmodels
yfinance
jupyter
```

Install with: `pip install -r requirements.txt`

Developed with Python 3.x. No GPU or specialized infrastructure required.

---

## Limitations

- **Window sensitivity:** Rolling beta estimates depend on the chosen window length (52-week primary, 26-week robustness). Shorter windows are noisier; longer windows lag in detecting regime shifts.
- **Linear measure:** Beta is a linear measure of systematic market sensitivity. It does not capture nonlinear exposures, tail risk, or asymmetric responses during sharp drawdowns.
- **Event definition:** Crisis period boundaries are defined manually. Different event start/end dates would produce different summary statistics.
- **Descriptive scope:** The analysis is descriptive. No formal hypothesis tests for structural breaks in beta are performed in the current version. Findings should not be interpreted as a trading or risk-management strategy.
- **ETF selection:** Results reflect the specific set of SPDR sector ETFs used. Different ETF proxies or factor decompositions could yield different conclusions.
- **Data coverage:** The sample runs 2018–2024. Patterns observed in this window may not generalize to other market cycles.

---

## Portfolio Notes

This project was completed as part of a quantitative finance coursework sequence. It demonstrates applied use of rolling regression, time-series return construction, macro data integration, and financial visualization in Python.

The analysis is intentionally scoped and does not overstate its conclusions. The primary contribution is a clean empirical framework for visualizing how sector market sensitivities evolve around identifiable macro and market events.

**TODO:** Add executable `.ipynb` notebook to `notebooks/` for full reproducibility.
