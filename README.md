# ML Prediction of African Population Growth Using Hydrology-Based Features

A machine learning project examining the extent to which water availability and climate variability shape population growth across African countries, developed for CSIS 320: Machine Learning at Siena University.

## Team

Daniel Montilla, Alejandro Perez Jorda, Iona Snape, Sidney Tovar
Advisor: Dr. Sharon G. Small

## Problem Statement

This project determines to what extent water availability and climate variability shape population growth in African countries. Through regression and machine learning, we evaluate whether hydrological data alone can be used to make accurate population growth predictions.

**Why it matters:** As the world's fastest-growing continent, Africa is largely overlooked in demographic modeling. Governments tend to respond reactively to demographic pressures, leading to population-related risks. Linking hydrological features to population trends could enable policymakers to proactively allocate resources before crises emerge.

**What we're predicting:** Annual population growth, based on hydrological features. Countries are grouped into dry, medium, and wet categories by average annual precipitation, since the structural relationship between water and population growth differs by climate regime.

## Data Set

- 1,351 rows — each row represents a single African country in a single year
- 54 African countries, 2001–2024
- 9 features, target variable: population growth
- **Features:** precipitation, river flow, soil water, surface water, terrestrial water storage, vegetation average, water bodies, average temperature, freshwater resources

## Data Preparation

- Removed special characters and whitespace; standardized feature names
- Dropped entirely empty rows; interpolated missing values where appropriate (median, linear)
- Converted categorical columns to numeric
- Removed COVID years (2020/21) to avoid distortion
- Created three precipitation-based bins (wet, medium, dry)
- Removed countries and non-hydrological variables (GDP, political violence) to avoid data leakage

## Feature Engineering

Each climate regime uses a distinct feature set tailored to the dynamics most relevant to it, so irrelevant variables don't dilute the predictive signal:

- **Wet region (productivity & flood variability):** vegetation change, river flow variability (3-yr SD), soil water deficit, 5-year precipitation trend
- **Mid region (climate anomalies):** temperature anomaly, precipitation anomaly, drought index, freshwater change
- **Dry region (water scarcity dynamics):** scarcity index, precipitation deficit, cumulative deficit (3-yr), river flow per vegetation, 3-year soil water trend

Tree-based models (Random Forest, LightGBM) were used, which are immune to collinearity — no variable-dropping was needed for that reason.

## Model & Training Setup

- **Training/validation:** rolling evaluation — for each test year *t*, trained on all years before *t* and validated on year *t*, ensuring zero data leakage (random testing wasn't feasible for this reason)
- **Testing:** final test held out on 2024

## Results

**Best model by region:** Random Forest (Wet), Random Forest (Medium), LightGBM (Dry)

| Region | Model | R² (test) | RMSE (test) | R² (train) | RMSE (train) |
|---|---|---|---|---|---|
| Wet | Random Forest | -0.33 | 0.4575 | -0.2373 | 0.2657 |
| Medium | Random Forest | 0.3833 | 0.5671 | 0.8253 | 0.2993 |
| Dry | LightGBM | 0.5644 | 0.7225 | 0.7325 | 0.4165 |

The medium-region model accurately tracked average population growth across 9 of 10 countries using hydrological features alone. South Sudan was a notable outlier — its population trends are driven by civil-conflict-related mass displacement, which no hydrological feature can capture.

Testing on a single held-out year (2024) was a statistically limited evaluation due to missing 2023/24 data and post-COVID demographic shifts; validation results are more robust and temporally stable. Positive test R² values outperform a naive baseline and explain a moderate share of variance from hydrological signals alone; the negative R² in the wet region is likely driven by Mauritius, which performed poorly due to data quality issues.

## Key Findings

- **Wet countries:** population growth is largely decoupled from hydrological signals — the model performs weakest here
- **Medium countries:** performed best — a hydrological "sweet spot" validating that water availability meaningfully shapes population growth
- **Dry countries:** moderate performance — constant water scarcity limits the model's ability to differentiate outcomes

## Limitations

- **Data quality:** African countries are systematically underrepresented in global datasets; missing values and inconsistent, infrequent reporting are common
- **Hydrology alone isn't always sufficient:** population growth is driven by a complex mix of cultural, political, economic, and health factors, and the model doesn't capture conflict-driven demographic shocks
- **Country heterogeneity:** hydrology statistics vary widely even within the same precipitation bin

## Future Improvements

- Expand beyond Africa to help the model learn more generalized hydrological-demographic relationships
- Incorporate non-hydrological features such as migration and conflict data, since hydrology alone can't explain all population growth and migration patterns
- Build a more granular, country-level model rather than a regional one

## Final Takeaway

Hydrological features across Africa can meaningfully contribute to predicting population growth.

## Repository Contents

- `Population_Growth_ML.ipynb` — main modeling notebook
- `2_Regression.ipynb` — regression analysis
- `ML_dataset2.csv`, `ML_water_dataset.csv` — datasets used
- `actual_vs_predicted.png` — model performance visualization

## Tools

Python (Pandas, NumPy, Scikit-learn, LightGBM)
