# 05 - MLOps & Model Monitoring

## Lifecycle Management

### 1. What is Model Drift?
**Answer:**
The degradation of model performance over time due to changes in the underlying data distribution or relationships between features and the target.
- **Data Drift:** Changes in input features (e.g., user behavior changes).
- **Concept Drift:** Changes in the target relationship (e.g., what was "fraud" yesterday is "normal" today).

### 2. How do you monitor a model in production?
**Answer:**
- **Performance Metrics:** Accuracy, Precision, Recall (if ground truth is available quickly).
- **Statistical Tests:** KS-Test, PSI (Population Stability Index) to detect drift in feature distributions.
- **System Metrics:** Latency, Throughput, Error rates.

### 3. What is CI/CD for Machine Learning (CT)?
**Answer:**
- **CI (Continuous Integration):** Testing code and data.
- **CD (Continuous Deployment):** Deploying the model serving service.
- **CT (Continuous Training):** Automatically retraining models when new data arrives or drift is detected.

## Evaluation Metrics

### 4. Precision vs. Recall vs. F1-Score?
**Answer:**
- **Precision:** Of all predicted positives, how many were actually positive? (Minimize False Positives).
- **Recall:** Of all actual positives, how many did we catch? (Minimize False Negatives).
- **F1-Score:** Harmonic mean of Precision and Recall. Use when you want a balance between both.

### 5. Explain the ROC-AUC Curve.
**Answer:**
- **ROC:** Plot of True Positive Rate (Recall) vs. False Positive Rate.
- **AUC:** Area Under the Curve. Represents the probability that a random positive instance is ranked higher than a random negative instance. AUC=1 is perfect; AUC=0.5 is random guessing.

### 6. What is A/B Testing for ML models?
**Answer:**
Deploying two versions of a model (A and B) to different subsets of users and comparing their performance on a business metric (e.g., Click-Through Rate) to decide which one is better.
