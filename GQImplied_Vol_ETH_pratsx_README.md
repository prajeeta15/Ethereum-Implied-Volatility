## Project Report: Forecasting Ethereum Implied Volatility with High-frequency market data

### 1. Overview

This project is for the prediction of Ethereum implied volatility (IV) based on the data on high-frequency market. Workflow contains both the machine learning (XGBoost) and classical time-series (ARIMA) methods that are used to create a submissions ready short-term volatility predictions and a performance measure.

### 2. Plugins and Programs

- Data Processing and Calculations: pandas, numpy
- Visualization: `matplotlib`, `seaborn`
- Machine Learning: `xgboost`, `shap` (to interpret)
- time-Series Modeling: `statsmodels` (ARIMA)
- Measurement & Evaluation: sklearn.metrics (MAE), scipy.stats (Pearson correlation)

These libraries were selected based on their efficiency in computation, interpretability and ability to process high frequency data of large quantities.

### 3. Feature Engineering

Relevant aspects relating to the microstructure of markets were inferred to enhance predictive performance:

- Spread: the difference between the best ask and best bid prices.
- Depth Imbalance: Imbalance between bid and offer volumes on top 2 levels.
- Log Return: Natural log change of the mid-prices which would show the price dynamics.
- Realized Volatility (30s window): Volatility measure tracked by computing rolling standard deviation of log returns to account to short-term volatility.

Such combination of features permits models to capture knowledge of price dynamics as well as the imbalances in the order book, which are essential in high frequency IV prediction.

### 4. XGBoost: Machine Learning

- Data Preparation: Feature and labels were split into training and testing. Any empty values have been completed and data have been cast as common.
- Model Training: XGBoost regression model was trained on the engineered features using moderate amount of estimators and limited depth to prevent the overfitting issue.
  Evaluation Metrics:

  **Pearson correlation** : it measures linear agreement between observed and forecasted IV.
  Mean Absolute Error (MAE) Expresses an average error of prediction.

Visualization: The IV was plotted actual vs. predicted in order to examine the quality of short-term prediction.

**Feature Interpretability**: The values of SHAP were calculated to quantify the importance of features, with the goal of diagnosing which microstructure signals are important to the model.

Latency due to inference: The average order of time of predictions was determined to make it suitable to fit in the near-real time applications.

### 5. ARIMA model Time-Series Modeling

**Downsampling**: To decrease the noise and computation, high-frequency IV was changed to a group of 10-second intervals.
**Rolling Forecasts**: ARIMA was fitted on rolling 10 window to forecast 30 seconds in advance.
Error Handling: NaNs were inserted when instances of forecast failures were encountered to be cleaned later on to be evaluated.
Evaluation Metrics: Equivalent to XGBoost--Pearson correlation, MAE and inference latency.
Visualization: the first 200 legitimate predictions were plotted on actual IV to determine the immediate quality of the forecasting.

_ARIMA offers a standard statistical comparison, which accompanies machine learning forecasts._

### 6. Kaggle Submission

The predicament: The submission limit in Kaggle is precisely 270,548 rows. ARIMA could only do a limited prediction of \~60k owing to computational expense.
Solution:

Predictions with XGBoost based on the entire data.
ARIMA-predict the first 60k rows (hybrid).
Other rows are filled with XGBoost estimations to get the amount of rows needed.
Data Cleaning: No NaNs exist, all are float, there is no index column, and float formatting is regular.
Invalidation: inspected CSV headers, data types, and na values to prevent such Kaggle errors as `Could not parse '' into expected type of Double`, `array length mismatch`.

This combination gives a good balance between accuracy provided by ARIMA and complete coverage and speed of XGBoost that makes the submission Kaggle friendly.

### 7. Problems and Resolutions

- NaN and empty string exceptions: NaN and `np.nan_to_num` and `fillna` were used to make sure that the data is numerically intact.
- Mismatch of array lengths: This was solved by filling XGBoost predictions of timestamps outside the range of the ARIMA.
- Key performance indicator latency: Measured and optimized to high frequency deployment.
- Interpretability: was addressed by SHAP tree to explain feature contributions of XGBoost.
- Visualization: Tended to check visualisations to prove trends that are predictable and find out the possible lag or bias.

### 8. Takeaways

1. Hybrid Modeling: ARIMA with XGBoost can be used to get the benefits of classic time-series learning, as well as non-linear machine learning.
2. Feature Importance: Spread, depth imbalance, and realized volatility are sizable predictors of short dated Ethereum IV.
3. Data Engineering and Data Matters: High-frequency data demands specific traits that are again pre-processed like rolling statistics, statistics returned and NaN processing.
4. Submission Compliance: Row Count, type correction and good CSV format are important when it comes to Kaggle assessments.
5. Scalability: Machine learning models, such as XGBoost are more scalable than the ARIMA to full high-frequency datasets, however, the ARIMA can perform a useful benchmarking role and hybrid forecasting.

### REAL LIFE ENV:

The ability to use high-frequency market data to produce shorter term correct implied volatility forecasts of Ethereum is worthwhile in the real world trading since it will enable decision making in time-bound trading. The XGBoost feature is used to capture complex, non-linear relationships both in the order book and in price dynamics and can be used in real-time to provide fast predictions, an important feature of market-making and execution strategies, and ARIMA component introduces a rigorous statistical view to capture short-term trends and dynamics. Collectively, they help traders to price options dynamically, hedge efficiently, control risk and optimize the provision of liquidity through low-latency volatility forecasts that have the ability to adapt to live market conditions. Putting together speed, interpretation and accuracy, the model is supposedly actionable directly in a trading system. So basically, it can quickly guess how jumpy Ethereum prices will be in the next few seconds. It looks at things like the difference between buying and selling prices, how many people are buying or selling, and recent price changes. Using this, it helps traders make smarter decisions, like setting option prices, deciding how much to buy or sell, and avoiding big losses, all very fast so it can keep up with the market.
