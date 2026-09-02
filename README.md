# PJM Hourly Load Forecasting

This project forecasts hourly electricity demand in the PJM East region and compares an interpretable linear model with a nonlinear gradient-boosting model and a seasonal benchmark. It demonstrates an end-to-end forecasting workflow: time-series quality checks, feature engineering, chronological validation, benchmark comparison, and business-oriented error interpretation.

## Business Question

How accurately can next-period electricity demand be estimated from calendar effects and recent load history, and how much value does a nonlinear model add over simple benchmarks?

Accurate load forecasts support generation planning, capacity management, procurement, and operating-risk decisions.

## Data

The analysis uses the `PJME_hourly.csv` series from the [Hourly Energy Consumption dataset on Kaggle](https://www.kaggle.com/datasets/robikscube/hourly-energy-consumption). The file contains hourly PJM East load in megawatts.

The raw data is not committed to this repository. See [`data/README.md`](data/README.md) for setup instructions.

## Methodology

1. Parse and sort hourly timestamps.
2. Average duplicated timestamps and restore the complete hourly index.
3. Interpolate isolated missing hours.
4. Create calendar features: month, weekday, hour, and U.S. holiday indicator.
5. Create 24-hour and 168-hour lag features.
6. Split chronologically at January 1, 2015 to avoid future-data leakage.
7. Compare three models on the same holdout period:
   - 168-hour seasonal-naive benchmark;
   - one-hot encoded linear regression;
   - XGBoost regression.

## Verified Results

| Model | RMSE (MW) | MAE (MW) | MAPE |
|---|---:|---:|---:|
| Seasonal naive, 168-hour lag | 4,645.3 | 3,400.2 | 10.540% |
| Linear regression | 2,520.5 | 1,883.9 | 5.959% |
| XGBoost | **2,426.6** | **1,792.5** | **5.612%** |

XGBoost reduced MAPE by approximately 47% relative to the weekly seasonal benchmark. The smaller improvement over linear regression suggests that calendar and lag features already explain a large share of the predictable variation, while nonlinear interactions provide an additional but more incremental benefit.

## Repository Structure

```text
pjme-load-forecasting/
├── README.md
├── requirements.txt
├── data/
│   └── README.md
└── src/
    └── train_models.py
```

## Run the Project

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
python src/train_models.py --data data/PJME_hourly.csv
```

The script prints the data-quality audit and model-comparison table and writes `results/model_metrics.csv`.

## Limitations and Next Steps

- Add weather, economic activity, and real-time operating variables.
- Use rolling-origin cross-validation rather than one fixed holdout period.
- Tune XGBoost using only past validation windows.
- Add prediction intervals and peak-load error analysis.
- Create a Power BI page for forecast-versus-actual and variance monitoring.

## Skills Demonstrated

Python, pandas, time-series validation, feature engineering, linear regression, XGBoost, forecasting metrics, model benchmarking, and analytical communication.
