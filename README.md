# NYC Taxi Trip Duration Prediction

Predicting how long a New York City taxi ride will take, from NYC Taxi and Limousine Commission trip records (January 2016).

## Data

Trip-level records stored in a SQLite database: pickup and dropoff timestamps, coordinates, passenger count, distance, and duration in seconds. Pulled into pandas with a SQL query that bounds pickup and dropoff coordinates to the New York area.

## Approach

**Selection and cleaning.** Limited the data to trips that both started and ended on Manhattan Island, since cross-borough trips follow different dynamics and would have muddied the model.

**EDA.** January 2016 was not a typical month. New Year's Day fell on a Friday, MLK Day shifted traffic patterns, and a historic blizzard shut down the city mid-month. I examined daily trip distributions to identify these outlier days and removed them, so the model trains on representative conditions rather than one-off events.

**Feature engineering.** Applied PCA to pickup and dropoff coordinates to partition Manhattan into regions that roughly match how the island is actually described geographically. Treated hour as categorical rather than continuous, since traffic does not scale linearly with the clock. Added day of week and trip distance.

**Model selection.** Compared a progression of models on held-out test data rather than training error:

| Model | Test RMSE (seconds) |
|---|---|
| Constant (predicts mean duration) | 399.14 |
| Simple linear (distance only) | 276.78 |
| Multiple linear (full design matrix) | 255.19 |
| Period-based tree regression | 246.63 |
| Speed prediction, converted to duration | 243.02 |
| **Tree regression on predicted speed** | **226.91** |

## Result

The final model cut RMSE 43% below the constant baseline. Two findings drove most of that gain. Fitting separate regressions per time period beat a single model with hour dummies, because the relationship between distance and duration itself changes by period rather than just shifting. And predicting speed and converting to duration outperformed predicting duration directly, since speed is closer to a linear function of the features while duration scales with distance.

## Stack

Python · SQL / SQLite · pandas · NumPy · scikit-learn · Matplotlib · Seaborn
