# Time-Varying Sector Betas Across Market Regimes

This project analyzes whether sector beta should be treated as a stable measure of market sensitivity or as a time-varying exposure that changes across market regimes.

Using daily and weekly data for major U.S. sector ETFs from 2018 to 2024, the project estimates static CAPM betas, 52-week rolling CAPM betas, and a 26-week robustness specification. The analysis evaluates how sector-level systematic risk changes during major market regimes, including the COVID crash, COVID recovery, 2022 bear market, Fed rate-hike cycle, regional banking crisis, and AI-driven technology rally.

## Research Questions

- Do sector betas vary meaningfully over time, or are static CAPM betas sufficient?
- Which sectors experience the largest beta changes during major market regimes?
- Do defensive sectors maintain lower and more stable betas during stress periods?
- Can rolling beta help explain or predict near-term realized sector volatility?

## Data

The analysis uses daily adjusted close prices for the following ETFs:

- XLK: Technology
- XLE: Energy
- XLF: Financials
- XLV: Health Care
- XLP: Consumer Staples
- XLU: Utilities
- XLY: Consumer Discretionary
- SPY: Market benchmark

Macroeconomic and regime variables include:

- 3-month Treasury bill rate
- Federal Funds Rate
- 10-year Treasury yield
- CPI
- VIX
- NBER recession indicator / manually defined regime windows

The sample period is January 2018 to December 2024.

## Methodology

The project follows five main steps:

1. Collect sector ETF and market benchmark price data.
2. Merge sector prices with macroeconomic variables and regime labels.
3. Construct daily log returns, excess returns, and weekly return panels.
4. Estimate static CAPM beta and 52-week rolling CAPM beta for each sector.
5. Test whether end-of-month rolling beta predicts next-month realized sector volatility.

The rolling CAPM model is:

```text
excess_return_sector = alpha + beta * excess_return_market + error
