# Changelog

All notable changes to the **VAT Risk Analysis -- Q1 2025** project are documented in this file.

The format is inspired by [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

---

## [1.0.0] -- 2025-02-13

### Added

- **Data ingestion** -- load and clean Q1 2025 sales data from `1Q_VAT.csv` (1 000 raw records, 898 after cleaning).
- **Currency conversion** -- batch-fetch daily exchange rates from the [Frankfurter API](https://api.frankfurter.app/) and convert all amounts to EUR.
- **Banker's rounding** -- apply `ROUND_HALF_UP` via Python `decimal` module to 3 decimal places for tax-precision arithmetic.
- **Checkpoint / backup** -- save intermediate EUR-converted dataset to `Data_in_EUR.csv` so that API calls can be skipped on re-runs.
- **Duplicate detection** -- flag transactions sharing the same (amount, currency, date, purchaser) tuple to prevent double-counting.
- **VAT rate compliance check** -- verify all rates match the legal categories (0 %, 9 %, 21 %).
- **Pivot table reports**
  - `VAT_TAX_base_data.csv` -- tax base by VAT rate and month (with totals).
  - `VAT_TAX_liability.csv` -- tax liability by VAT rate and month (excluding 0 % exempt).
- **Visualisations**
  - Pie chart -- distribution of transactions across VAT rate categories.
  - Grouped bar chart -- net sales & VAT by original currency (EUR-normalised).
  - Pareto chart -- sales by purchaser with cumulative-% line and 80 % threshold.
  - Bar chart -- month-over-month net sales for Q1 2025 (two variants).
- **Statistical analysis**
  - Descriptive statistics (mean, standard deviation, skewness).
  - One-way ANOVA (F-test) across monthly means (p = 0.98 -- no significant difference).
  - Z-score outlier detection (threshold: 3 std) -- none detected.
- **Inline code comments** -- detailed explanatory comments added to every notebook cell.
- `README.md` with project overview, data dictionary, requirements, how-to-run guide, and key results.
- `CHANGELOG.md` (this file).

### Environment

| Component | Version |
|---|---|
| Python | 3.13.5 |
| pandas | latest via pip |
| matplotlib | latest via pip |
| seaborn | latest via pip |
| scipy | latest via pip |
| requests | latest via pip |
| Jupyter Notebook | ipykernel 3 |
