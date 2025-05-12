# Rice Yield Prediction Using LSTM and Regression

## Overview
This project aims to predict rice yield by combining time series forecasting with regression techniques. We leverage weather data from the Korea Meteorological Administration and rice production statistics (based on Jeollanam-do reports) to forecast future yields. The approach consists of two stages:
1. **Intermediate predictions:** Forecast future weather conditions and cultivated area using time series models.
2. **Final prediction:** Use these forecasts as input features in a regression model (e.g., XGBoost or Random Forest) to predict rice yield.

---

## 1. Data Collection
- **Weather Data (Monthly):**
  - Collected over 10 years from 6 major stations (e.g., Mokpo, Yeosu, Wando, Jangheung, Haenam, Goheung).
  - **Key Features:**  
    - Average Temperature  
    - Monthly Precipitation  
    - Sunshine Duration  
    - Sea-Level Pressure  
    - Vapor Pressure  
    - Relative Humidity  
    - Wind Speed  
    - Total Sunshine Hours
- **Rice Data (Annual):**
  - Derived from Jeollanam-do statistical reports.
  - **Metrics:** Rice yield and cultivated area.
- **Additional Details:**
  - Raw data is inspected for format consistency, missing values, and potential outliers.
  - Collection metadata such as timestamp, location, and measurement units are recorded for later reference.

---

## 2. Preprocessing & Feature Engineering
- **Data Cleaning & Conversion:**
  - Convert date fields (e.g., '일시') to datetime types.
  - Filter data to include only the desired weather stations (Mokpo, Yeosu, Wando, Jangheung, Haenam, Goheung).
  - Use only data from 2015 onward to reflect recent trends.
- **Feature Engineering:**
  - Transform month information into cyclical features using sine and cosine functions:
    - Create `month_sin` and `month_cos` features.
- **Missing Value Handling:**
  - Apply linear interpolation to numerical columns to address missing values.
- **Note:**  
  - While grouping by the first day of the month can be useful for later time series grouping, it is not included in the initial preprocessing.

---

## 3. Time Series Dataset Composition & Normalization (for Intermediate Models)
- **Dataset Construction:**
  - Use a sliding window approach (look_back = 12) to create input sequences (X) and target (Y) datasets.
  - Split the dataset into training (80%) and testing (20%) subsets.
- **Normalization:**
  - Fit a MinMaxScaler on the training data and transform both training and testing sets to standardize numerical features.
- **Reference:**  
  - For subsequent grouping, data may later be arranged by the first day of the month as needed.

---

## 4. Intermediate Models: Time Series Forecasting
- **(1) Weather Forecasting Model:**
  - **Method:**  
    - Use an LSTM model to capture temporal patterns and interactions in the multivariate weather data.
  - **Hyperparameter Tuning:**  
    - Employ a grid search (varying parameters such as the number of neurons, dropout rate, learning rate, and optimizer) for a brief number of epochs to select the optimal configuration.
  - **Rationale:**  
    - LSTM is well-suited for modeling complex time-dependent relationships in multivariate data.
- **(2) Cultivated Area Forecasting Model:**
  - **Method:**  
    - Apply a univariate forecasting model (such as ARIMA or simple regression) to predict future cultivated area from annual data.
  - **Rationale:**  
    - The cultivated area data is relatively stable over time, making it amenable to simpler time series models.
- **Utilization:**  
  - The forecasts from these intermediate models (future weather data and cultivated area predictions) will serve as input features for the final rice yield prediction.

---

## 5. Final Model: Rice Yield Prediction
- **Objective:**  
  - Predict rice yield (the final target) using a regression approach rather than time series forecasting.
- **Input Features:**
  - **Weather Variables:**  
    - Focus on key weather metrics during the critical cultivation period (e.g., data from April to September) and, if relevant, include the forecasted weather data.
  - **Cultivated Area:**  
    - Incorporate both historical values and the predicted cultivated area from the intermediate model.
- **Approach:**
  - Use non-linear regression models such as XGBoost or Random Forest.
- **Rationale:**
  - These models are effective in capturing the complex, non-linear relationships between the predictors (weather and area) and the rice yield.
  - They also allow for feature importance analysis to understand the influence of each variable.
- **Evaluation:**
  - Assess model performance using cross-validation and metrics such as MSE, MAE, and R².

---

## 6. Summary & Future Work
- **Overall Pipeline Summary:**
  1. **Data Collection:** Gather weather and rice production data (Jeollanam-do statistics).
  2. **Preprocessing & Feature Engineering:** Clean and transform data for analysis.
  3. **Time Series Composition & Normalization:** Prepare the dataset for intermediate forecasting.
  4. **Intermediate Forecasting:** Predict future weather conditions (via LSTM) and cultivated area (via ARIMA/regression).
  5. **Final Regression Modeling:** Predict rice yield using the forecasted inputs.
- **Strengths:**
  - Hierarchical modeling strategy enabling an assessment of uncertainty at each stage.
  - Robust data preprocessing and normalization for stable model training.
- **Future Enhancements:**
  - Evaluate the impact of intermediate model prediction errors (error propagation) quantitatively.
  - Incorporate extensive cross-validation and compare performance with advanced time series models (e.g., Transformer-based approaches).

---

## Final Goal
Leverage forecasted weather data and predicted cultivated area to build an integrated regression model (e.g., using XGBoost or Random Forest) that effectively predicts rice yield as per Jeollanam-do statistical data, thereby providing valuable insights for agricultural planning.
