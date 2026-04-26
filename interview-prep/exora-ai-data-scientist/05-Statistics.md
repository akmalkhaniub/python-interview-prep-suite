# 05: Statistics & Metric Selection

Exora AI values strong analytical and problem-solving skills.

## 1. Probability & Statistics
- **P-Value:** The probability of seeing the data if the null hypothesis is true. A low p-value (< 0.05) suggests the result is statistically significant.
- **Correlation vs. Causation:** Correlation does not mean one thing causes the other.
- **Central Limit Theorem:** Why we can assume many distributions are "Normal" when the sample size is large.

## 2. Classification Metrics
- **Accuracy:** Good for balanced data.
- **Precision:** "Of all predicted positive, how many were actually positive?" (Important for Spam detection).
- **Recall (Sensitivity):** "Of all actual positive, how many did we find?" (Important for Cancer detection).
- **F1-Score:** Harmonic mean of Precision and Recall.
- **ROC-AUC:** Area under the curve. Measures the model's ability to distinguish between classes regardless of threshold.

## 3. Regression Metrics
- **MAE (Mean Absolute Error):** Easy to interpret (average error).
- **RMSE (Root Mean Squared Error):** Penalizes larger errors more heavily.
- **R-Squared:** How much of the variance is explained by the model.

## 4. Hypothesis Testing
- **A/B Testing:** Comparing two versions of a product.
- **T-test / ANOVA:** Comparing means across groups.
- **Chi-Squared Test:** Comparing categorical variables.

## 5. Potential Interview Questions
1. **In a Fraud Detection model, would you prioritize Precision or Recall?**
   - *Answer:* **Recall.** It's better to flag some legit transactions as fraud (and investigate them) than to miss a single fraudulent transaction. However, the balance depends on the cost of investigation.
2. **Explain the P-value to a non-technical stakeholder.**
   - *Answer:* "It's a measure of how 'surprising' our results are. If the p-value is very low, it means our findings are likely real and not just a lucky coincidence."
3. **What is the "Curse of Dimensionality"?**
   - *Answer:* As the number of features (dimensions) increases, the volume of the space increases so fast that the data becomes sparse. This makes distance-based algorithms (like KNN) perform poorly.
