# Public Utility Regression Model

A Python/Colab notebook that models a public utility's monthly **revenue** as a function of **production** and seasonal effects, using linear regression (OLS) with train/test splits and MAPE-based accuracy assessment.

## Overview

The notebook builds two competing regression models — one capturing a **spring** seasonal effect and one capturing a **summer** seasonal effect — to predict monthly revenue from production volume. Each model is trained on a historical subset of the data and evaluated on a held-out test period, using Mean Absolute Percentage Error (MAPE) as the accuracy metric.

## Data

**Source file:** `AICPA_regressionAnalysisData.csv`

**Columns:**
| Column | Description |
|---|---|
| `type` | Split label: `dt4training` or `dt4testing` |
| `date` | Month-end date of the observation |
| `revenue` | Monthly revenue (target variable) |
| `production` | Monthly production volume |
| `coolDD` | Cooling degree days for the month |
| `heatDD` | Heating degree days for the month |

**Train/test split:**
- Training set (`dt4training`): 2011–2013 (36 monthly observations)
- Testing set (`dt4testing`): 2014 (12 monthly observations)

## Feature Engineering

Two seasonal dummy variables and their interaction terms with `production` are constructed from the `date` column:

- **`spring_DV`** — 1 if the month is March, April, or May; else 0
- **`spring_interaction`** — `production × spring_DV`
- **`summer_DV`** — 1 if the month is June, July, or August; else 0
- **`summer_interaction`** — `production × summer_DV`

These interaction terms let the slope of the revenue–production relationship differ between the seasonal period and the rest of the year.

## Models

Both models are fit with `statsmodels.api.OLS`, using `production`, a seasonal dummy, and the seasonal interaction term as regressors (plus a constant).

### 1. Spring Model
- **Predictors:** `production`, `spring_DV`, `spring_interaction`
- **Fitted coefficients (from the training data):**

| Term | Coefficient |
|---|---|
| const | 5,645,359.67 |
| production | 17.00 |
| spring_DV | -6,781,361.28 |
| spring_interaction | 23.65 |

- **Test-set MAPE:** ≈ 28.93%

### 2. Summer Model
- **Predictors:** `production`, `summer_DV`, `summer_interaction`
- **Fitted coefficients (from the training data):**

| Term | Coefficient |
|---|---|
| const | 1,244,884.00 |
| production | 30.40 |
| summer_DV | 4,207,869.63 |
| summer_interaction | -17.81 |

- **Test-set MAPE:** ≈ 19.68%

Based on these MAPE scores, the **summer model** fits the 2014 test data more closely than the spring model.

## Evaluation Methodology

For each model:
1. Fit on the training set via OLS.
2. Generate predictions on the test set.
3. Compute absolute percentage error per observation: `|actual − predicted| / actual`.
4. Average the absolute percentage errors to get the model's MAPE.

## Visualizations

The notebook plots actual vs. predicted revenue over the 2014 test period for each model:
- **Spring model:** actual revenue (green) vs. predicted revenue (blue)
- **Summer model:** actual revenue (green) vs. predicted revenue (red)

## Requirements

```
pandas
numpy
matplotlib
statsmodels
```

## How to Run

1. Open the notebook in Google Colab (or a local Jupyter environment).
2. Ensure `AICPA_regressionAnalysisData.csv` is available in the working directory (or upload it to the Colab session).
3. Run all cells in order — the notebook proceeds through data loading, feature engineering, train/test split, model fitting, prediction, MAPE scoring, and plotting.

## Notes

- This README was generated from the notebook's exported PDF/print view rather than the live `.ipynb` file, so cell outputs (coefficients, MAPE values) reflect what was captured in that export.
- The original Colab file can be found at the link embedded in the export header; access requires appropriate permissions.
