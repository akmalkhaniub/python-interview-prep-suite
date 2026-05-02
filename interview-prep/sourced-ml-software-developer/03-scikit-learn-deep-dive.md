# 03 - Scikit-Learn Deep Dive

## API and Workflow

### 1. What is a Scikit-Learn `Pipeline`?
**Answer:**
A tool to chain multiple estimators into one. It automates the workflow of preprocessing followed by an estimator.
- **Benefits:** Prevents data leakage (ensures scaling is only fit on training data during CV) and makes code cleaner/reproducible.
- **Example:** `Pipeline([('scaler', StandardScaler()), ('svc', SVC())])`.

### 2. Difference between `fit()`, `transform()`, and `fit_transform()`?
**Answer:**
- **fit():** Calculates parameters (e.g., mean and std for scaling) and saves them internally.
- **transform():** Applies the calculated parameters to the data.
- **fit_transform():** Does both in one step. Efficient for training data. Only `transform()` should be used on test data to avoid leakage.

### 3. How do you perform Hyperparameter Tuning in Scikit-Learn?
**Answer:**
- **GridSearchCV:** Exhaustive search over a specified parameter grid.
- **RandomizedSearchCV:** Samples parameters from a distribution; faster and often as effective as Grid Search.
- **HalvingGridSearchCV:** Successive halving to find best parameters faster.

## Specific Algorithms

### 4. How does Random Forest work?
**Answer:**
An ensemble method that builds multiple Decision Trees and merges them (Bagging).
- **Key Idea:** Each tree is built on a random bootstrap sample of the data and uses a random subset of features for each split. This reduces variance and prevents overfitting.

### 5. What are the key hyperparameters for an SVM?
**Answer:**
- **C:** Regularization parameter. Small C -> wider margin (more misclassifications allowed); Large C -> narrower margin (harder classification).
- **Kernel:** Type of boundary (linear, poly, rbf, sigmoid).
- **Gamma:** Defines how far the influence of a single training example reaches (for RBF).

### 6. Explain the concept of "Ensemble Learning."
**Answer:**
Combining multiple models to achieve better performance than any single model.
- **Bagging:** Parallel (Random Forest).
- **Boosting:** Sequential (AdaBoost, Gradient Boosting, XGBoost).
- **Stacking:** Uses a meta-model to combine predictions of base models.
