# 01 - ML Fundamentals

## Core Concepts

### 1. Explain the Bias-Variance Tradeoff.
**Answer:**
- **Bias:** Error due to overly simplistic assumptions in the learning algorithm. High bias leads to **underfitting** (the model misses relevant relations).
- **Variance:** Error due to too much complexity in the learning algorithm. High variance leads to **overfitting** (the model models the noise in the training data).
- **Tradeoff:** Increasing model complexity decreases bias but increases variance. The goal is to find the "sweet spot" that minimizes total error.

### 2. Difference between Supervised and Unsupervised Learning?
**Answer:**
- **Supervised:** Learning from labeled data (input-output pairs). Tasks include Classification and Regression.
- **Unsupervised:** Learning from unlabeled data to find hidden patterns or structures. Tasks include Clustering (K-Means), Dimensionality Reduction (PCA), and Association.

### 3. What is Cross-Validation and why use it?
**Answer:**
Cross-validation (e.g., K-Fold) is a technique to evaluate a model's performance by splitting the data into multiple folds. Each fold serves as a validation set while the others are used for training.
- **Why:** It provides a more robust estimate of model performance than a single train-test split and helps detect overfitting.

### 4. Explain Gradient Descent.
**Answer:**
An optimization algorithm used to minimize the cost function by iteratively moving in the direction of the steepest descent (negative of the gradient).
- **Learning Rate:** A hyperparameter that determines the step size at each iteration. Too large -> may skip the minimum; too small -> slow convergence.

### 5. What are Generative vs. Discriminative models?
**Answer:**
- **Discriminative:** Learns the boundary between classes (e.g., Logistic Regression, SVM, Random Forest). It models $P(y|x)$.
- **Generative:** Learns the distribution of individual classes (e.g., Naive Bayes, GANs). It models $P(x|y)$ and uses Bayes' rule to find $P(y|x)$.
