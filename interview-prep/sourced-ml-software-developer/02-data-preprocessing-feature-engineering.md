# 02 - Data Preprocessing & Feature Engineering

## Data Cleaning & Transformation

### 1. How do you handle missing values in a dataset?
**Answer:**
- **Deletion:** Remove rows or columns with missing values (if the data is MCAR - Missing Completely At Random).
- **Imputation:**
    - Mean/Median/Mode imputation.
    - Predictive imputation (e.g., using KNN or Regression to fill gaps).
    - Categorical: Use a "Missing" label.
- **Why:** Most ML algorithms (except some tree-based ones like XGBoost) cannot handle `NaN` values.

### 2. Difference between Standardization and Normalization?
**Answer:**
- **Standardization (Z-score):** Rescales data to have a mean of 0 and standard deviation of 1. Formula: $z = (x - \mu) / \sigma$. Good for algorithms assuming Gaussian distribution (SVM, Logistic Regression).
- **Normalization (Min-Max Scaling):** Rescales data to a fixed range (usually [0, 1]). Formula: $x' = (x - min) / (max - min)$. Good for algorithms sensitive to scale like KNN or Neural Networks.

### 3. How to handle Categorical Variables?
**Answer:**
- **One-Hot Encoding:** Creates binary columns for each category. Use for nominal data (no order). Beware of the "Dummy Variable Trap."
- **Label Encoding:** Assigns an integer to each category. Use for ordinal data (has order).
- **Target Encoding:** Replaces categories with the mean target value for that category.

## Feature Engineering

### 4. What is Feature Selection and why is it important?
**Answer:**
Selecting a subset of relevant features for model construction.
- **Methods:** Filter (Correlation, Chi-Square), Wrapper (Recursive Feature Elimination), Embedded (Lasso/L1 regularization).
- **Importance:** Reduces overfitting, improves accuracy, and reduces computational cost.

### 5. Explain PCA (Principal Component Analysis).
**Answer:**
A dimensionality reduction technique that transforms features into a new set of orthogonal (uncorrelated) variables called Principal Components, which capture the maximum variance. It is an unsupervised technique.

### 6. How do you handle imbalanced datasets?
**Answer:**
- **Resampling:** Oversampling the minority class (SMOTE) or Undersampling the majority class.
- **Metrics:** Use Precision-Recall, F1-Score, or AUC-ROC instead of Accuracy.
- **Algorithms:** Use cost-sensitive learning or tree-based algorithms that handle imbalance better.
