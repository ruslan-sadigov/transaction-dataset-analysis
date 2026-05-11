# Transaction Dataset Analysis

End-to-end analysis of a 1,000,000-row transaction dataset spanning April 2022 to December 2024, covering exploratory data analysis, customer segmentation, organization co-occurrence patterns, time-series forecasting, and hierarchical clustering.

## Overview

This project explores a transactional dataset of authentication/acknowledgement requests between persons and organizations. The analysis covers five major phases:

1. **Data Loading & Cleaning** — schema parsing, null handling, duplicate ID investigation
2. **Exploratory Data Analysis** — volume trends, organizational dominance, spike investigation
3. **Customer Segmentation (RF Analysis)** — segmenting 235,506 persons by recency and frequency
4. **Organization Co-occurrence Analysis** — frequent itemset mining with FP-Growth
5. **Time-Series Forecasting** — SARIMA, Auto ARIMA, and Chronos foundation model comparison
6. **Organization Clustering** — hierarchical clustering with Ward linkage

## Dataset

| Attribute | Value |
|---|---|
| Total rows | 1,000,000 |
| Date range | April 14, 2022 - December 31, 2024 |
| Unique organizations | 95 |
| Unique persons | 235,506 |

### Schema

| Column | Type | Description |
|---|---|---|
| Id | Integer | Transaction ID (non-unique, recycled) |
| OrgId | Integer | Organization identifier |
| TransactionTypeId | Integer | Type of transaction (1 or 2) |
| Transaction_Date | Datetime | Timestamp of the transaction |
| OperationStatusId | Integer | Status code (10=Acknowledged, 20=Refused) |
| CardlNumber | String | Card-like identifier (nullable) |
| PersonId | Integer | Person identifier |

## Key Findings

- **Extreme concentration:** Org 49 alone accounts for 62% of transactions and 69% of unique persons
- **Strong temporal patterns:** Clear weekly seasonality and exponential growth trend
- **ID column is unreliable:** Transaction IDs are recycled with a typical 240-day gap between reuses
- **Chronos outperforms SARIMA:** MAPE 25.15% vs 32.98% for manual SARIMA, with zero tuning
- **Four organizational clusters:** Dominant (1 org), High Volume (4 orgs), Regular (87 orgs), Inactive/Problematic (3 orgs)

## Setup

### Prerequisites
- Python 3.10+
- Windows / Linux / macOS

### Installation

1. Clone the repository
```bash
git clone <repo-url>
cd transaction-dataset
```

2. Create and activate a virtual environment
```bash
python -m venv venv

# Windows (PowerShell)
.\venv\Scripts\Activate.ps1

# Linux / macOS
source venv/bin/activate
```

3. Install dependencies
```bash
pip install notebook
```

The notebook itself installs analysis libraries on first run via:
```python
%pip install pandas numpy matplotlib seaborn scikit-learn statsmodels pmdarima mlxtend chronos-forecasting torch
```

### Running the Notebook

Launch Jupyter:
```bash
jupyter notebook
```

Open `transaction_dataset_analysis.ipynb` and run cells sequentially.

For VS Code users: open the notebook, select the venv kernel, and run.

## Project Structure

```
transaction-dataset/
├── transaction_dataset_analysis.ipynb   # Main analysis notebook
├── data/
│   ├── transactions.csv                  # Raw transaction data
│   └── operation_statuses.csv            # Status lookup table
├── report.tex                            # LaTeX analysis report
├── report.pdf                            # Compiled report
├── assets/                               # Exported figures (PDF)
├── .gitignore
└── README.md
```

## Methodology Highlights

### RF Segmentation
Five segments derived from manually-binned Recency and Frequency scores:
- **Champions** (9,590) — recent and heavy users
- **Loyal** (44,850) — active and regular
- **At Risk** (17,691) — previously active, now fading
- **Lost** (43,011) — inactive and low usage
- **Occasional** (120,364) — casual users

### Time-Series Forecasting
Three approaches compared on a 30-day test set:

| Model | MAE | RMSE | MAPE |
|---|---|---|---|
| Manual SARIMA (1,1,1)(1,1,1,7) | 1040.93 | 1614.83 | 32.98% |
| Auto ARIMA (4,1,2)(2,0,[1],7) | 1207.19 | 1763.48 | 36.96% |
| **Chronos (chronos-t5-small)** | **799.36** | **1266.05** | **25.15%** |

### Organization Clustering
Hierarchical clustering (Ward linkage) on 4 features:
- Total transactions
- Refusal rate
- Unique persons
- Average daily transactions

Resulted in 4 interpretable clusters with clear behavioral profiles.

## Libraries Used

| Library | Purpose |
|---|---|
| pandas, numpy | Data manipulation |
| matplotlib, seaborn | Visualization |
| scikit-learn | Scaling, clustering metrics |
| scipy | Hierarchical clustering |
| statsmodels | SARIMA, decomposition, ADF, ACF/PACF |
| pmdarima | Auto ARIMA |
| mlxtend | FP-Growth, association rules |
| chronos-forecasting | Foundation time-series model |
| torch | Chronos backend |

## Report

The full written analysis is available in `report.tex` (LaTeX source) and `report.pdf` (compiled output). Compile with:

```bash
pdflatex report.tex
```

## Author

Ruslan Sadigov
