# VAT Risk Analysis -- Q1 2025

A data analysis project that processes Q1 2025 sales transaction data, converts multi-currency amounts to EUR using live exchange rates, calculates VAT liabilities, and performs statistical risk assessment including outlier detection with hypothesis testing and distribution analysis.

## Overview

This project automates the end-to-end workflow of a quarterly VAT risk review:

1. **Data Ingestion & Cleaning** -- Loads raw sales data from CSV, drops incomplete records (stornos, entry errors, unearned revenue), and normalises column names.
2. **Currency Conversion** -- Fetches daily exchange rates from the [Frankfurter API](https://api.frankfurter.app/) and converts all transaction amounts to EUR. Requests are batched by unique `(date, currency)` pairs to minimise API calls.
3. **Tax-Precision Rounding** -- Applies banker's rounding (`ROUND_HALF_UP`) to three decimal places for all EUR monetary values and VAT amounts.
4. **Data Validation**
   - Duplicate detection -- prevents double-counting of revenue in monthly totals.
   - VAT rate compliance -- verifies that every rate matches one of the legal categories (0 %, 9 %, 21 %).
5. **Reporting**
   - Pivot table of the VAT tax base segmented by rate and month.
   - Pivot table of the VAT tax liability segmented by rate and month (excluding tax-exempt 0 % transactions).
6. **Visualisation**
   - Pie chart -- distribution of transactions across VAT rate categories.
   - Bar chart -- month-over-month net sales volume for Q1 2025.
7. **Statistical Analysis**
   - Descriptive statistics (mean, standard deviation, skewness) characterising the transaction profile.
   - One-way ANOVA (F-test) testing whether mean transaction values differ significantly across the three months.
   - Z-score-based outlier identification (threshold: 3 standard deviations).

## Project Structure

```
.
├── 1Q_VAT.csv                  # Raw input -- Q1 2025 sales transactions
├── Project_VAT_risk.ipynb      # Main analysis notebook
├── README.md                   # This file
└── CHANGELOG.md                # Changelog / version history
```

### Generated Outputs

Running the notebook produces the following artefacts:

| File | Description |
|---|---|
| `Data_in_EUR.csv` | Cleaned dataset with EUR-converted amounts and VAT |
| `VAT_TAX_base_data.csv` | Pivot table -- tax base by VAT rate and month |
| `VAT_TAX_liability.csv` | Pivot table -- tax liability by VAT rate and month |
| `1Q2025_monthly_sales.png` | Bar chart of monthly net sales |

## Input Data

The source file `1Q_VAT.csv` contains **1 000 records** with the following columns:

| Column | Type | Description |
|---|---|---|
| `transaction_ID` | int | Unique transaction identifier |
| `purchaser` | str | Buyer company name (e.g. Company A--E) |
| `amount_without_VAT` | float | Net transaction amount (before VAT) |
| `currency` | str | Transaction currency (CZK, EUR, GBP, USD, ...) |
| `rate_VAT` | float | VAT rate applied (0.00, 0.09, 0.21) |
| `date` | str | Transaction date (M/D/YYYY) |

After cleaning (removing rows where `amount_without_VAT` is NaN), **898 records** remain for analysis.

## Requirements

The project uses **Python 3.10+** and the following libraries:

| Package | Purpose |
|---|---|
| **pandas** | Data manipulation, pivot tables, CSV I/O |
| **matplotlib** | Static visualisations (bar charts, pie charts) |
| **seaborn** | Plot styling and aesthetics |
| **scipy** | Statistical tests (one-way ANOVA via `scipy.stats.f_oneway`) |
| **requests** | HTTP calls to the Frankfurter exchange-rate API |
| **decimal** *(stdlib)* | Banker's rounding (`ROUND_HALF_UP`) for tax-precision arithmetic |

## How to Run

### 1. Prerequisites

Make sure you have **Python 3.10+** installed. You can verify with:

```bash
python --version
```

### 2. Install dependencies

```bash
pip install pandas matplotlib seaborn scipy requests
```

All other imports (`decimal`, `math`) come from the Python standard library.

### 3. Launch the notebook

You can use **Jupyter Notebook**, **JupyterLab**, or **VS Code / Cursor** with the Jupyter extension:

```bash
# Option A – classic Jupyter Notebook
jupyter notebook Project_VAT_risk.ipynb

# Option B – JupyterLab
jupyter lab Project_VAT_risk.ipynb

# Option C – run all cells from the command line (no browser needed)
jupyter nbconvert --to notebook --execute Project_VAT_risk.ipynb --output Project_VAT_risk_executed.ipynb
```

### 4. Execute

1. Make sure `1Q_VAT.csv` is in the **same directory** as the notebook.
2. Open the notebook and **Run All Cells** sequentially (Kernel → Restart & Run All).
3. Wait for the currency-conversion step (cell 7) -- it makes HTTP requests to the Frankfurter API, so an **active internet connection** is required on the first run.
4. Once complete, all output CSVs and PNG charts will be saved to the working directory.

### 5. Skip the API on subsequent runs (optional)

After the first successful execution, a checkpoint file `Data_in_EUR.csv` is written to disk. You can load it directly instead of re-fetching exchange rates:

```python
df = pd.read_csv('Data_in_EUR.csv')
```

Replace the API cell (cell 7) with the line above, then continue from cell 8 onward.

## Key Results (Q1 2025)

| Metric | Value |
|---|---|
| Total net sales (EUR) | ~4 191 550 |
| Total VAT liability (EUR) | ~825 475 |
| Mean transaction value | ~4 668 EUR |
| Standard deviation | ~3 088 EUR |
| Skewness | ~0.07 (fairly symmetrical) |
| ANOVA p-value (monthly means) | 0.98 (no significant difference) |
| Outliers (Z > 3) | None detected |

## License

This project is provided for educational and analytical purposes.
