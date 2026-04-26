# 01: ML Fundamentals & Optimization

Classical Machine Learning remains the backbone of most industrial AI applications.

## 1. Supervised Learning
- **Regression:** Predicting continuous values (Linear, Ridge, Lasso).
- **Classification:** Predicting categories (Logistic, SVM, Random Forest, Gradient Boosting).
- **Ensemble Methods:** Bagging (Random Forest) vs. Boosting (XGBoost, CatBoost). Boosting usually provides higher accuracy but is more prone to overfitting.

## 2. Unsupervised Learning
- **Clustering:** Grouping similar data points (K-Means, DBSCAN).
- **Dimensionality Reduction:** Reducing feature space while preserving variance (PCA, t-SNE). Useful for visualization and speeding up training.

## 3. Model Optimization Techniques
- **Hyperparameter Tuning:** 
    - **Grid Search:** Exhaustive search (Slow).
    - **Random Search:** Faster, often finds better results in less time.
    - **Bayesian Optimization:** Uses previous results to choose the next set of parameters (Optuna).
- **Regularization:** 
    - **L1 (Lasso):** Can lead to sparse weights (feature selection).
    - **L2 (Ridge):** Penalizes large weights (prevents overfitting).

## 4. Cross-Validation
- **K-Fold:** Splitting data into K parts and training K times to ensure the model generalizes well.
- **Stratified K-Fold:** Ensures each fold has the same percentage of samples for each target class (Essential for imbalanced data).

## 5. Potential Interview Questions
1. **Explain the Bias-Variance Tradeoff.**
   - *Answer:* **Bias** is error from erroneous assumptions (Underfitting). **Variance** is error from sensitivity to small fluctuations in training data (Overfitting). We aim for a "Sweet Spot" that minimizes both.
2. **What is the difference between Random Forest and Gradient Boosting?**
   - *Answer:* Random Forest builds trees in parallel (Bagging) and averages them. Gradient Boosting builds trees sequentially (Boosting), where each tree tries to correct the errors of the previous one.
3. **How do you handle imbalanced datasets?**
   - *Answer:* Use better metrics (F1, AUC-ROC instead of Accuracy). Resample data (SMOTE for oversampling, or undersampling). Use class weights in the loss function.
