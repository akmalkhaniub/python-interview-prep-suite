# 03: Data Preprocessing & EDA

"High data quality and reliability" is a core responsibility at Exora AI.

## 1. Handling Missing Values
- **Deletion:** Removing rows/columns (only if data is missing at random and small).
- **Imputation:** Filling with Mean/Median/Mode, or using a model (KNN Imputer).
- **Indicator Variable:** Adding a column `is_missing` to preserve the fact that data was absent.

## 2. Outlier Detection
- **Z-Score:** Measuring how many standard deviations a point is from the mean.
- **IQR (Interquartile Range):** Points outside `[Q1 - 1.5*IQR, Q3 + 1.5*IQR]`.
- **Isolation Forest:** An unsupervised ML model specifically for anomaly detection.

## 3. Feature Engineering
- **Categorical Encoding:** One-Hot Encoding (for low cardinality), Label Encoding (for ordinal data), Target Encoding.
- **Scaling:** **Normalization** (Min-Max, 0 to 1) vs **Standardization** (Z-score, Mean 0, Std 1). Standardization is generally preferred for algorithms like SVM or Neural Networks.

## 4. EDA (Exploratory Data Analysis)
- **Visuals:** Heatmaps (Correlation), Histograms (Distribution), Boxplots (Outliers).
- **Statistical Summaries:** `df.describe()`, skewness, kurtosis.

## 5. Potential Interview Questions
1. **Should you scale features before or after splitting into Train/Test?**
   - *Answer:* **After.** You should calculate the mean/std on the Train set only, then use those values to transform both Train and Test. Doing it before causes "Data Leakage".
2. **How do you handle a feature with high cardinality (e.g., "City Name")?**
   - *Answer:* One-hot encoding would create too many columns. I'd use Target Encoding, Frequency Encoding, or group rare cities into an "Other" category.
3. **What is "Data Leakage" and how do you prevent it?**
   - *Answer:* Leakage happens when information from the future (or the target) is present in the training features. Prevention: Strict train-test separation, careful time-series splitting, and ensuring features are calculated only from historical data.
