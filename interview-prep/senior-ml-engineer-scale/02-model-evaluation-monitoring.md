# 02 - Model Evaluation & Monitoring

## Evaluation Frameworks

### 1. How do you evaluate a model that doesn't have immediate ground truth?
**Answer:**
- **Proxy Metrics:** Monitor intermediate signals (e.g., in a recommender, use Click-Through Rate as a proxy for long-term satisfaction).
- **Human-in-the-loop:** Periodically sample predictions and have human experts label them.
- **Self-Supervised Consistency:** Check if the model gives consistent answers for augmented versions of the same input.

### 2. What is "Shadow Deployment"?
**Answer:**
Deploying a new model version alongside the production model. The shadow model receives live traffic and makes predictions, but its outputs are only logged and not shown to the user. 
- **Goal:** Validate performance and stability on real data without risk.

### 3. Difference between Model Validation and Model Monitoring?
**Answer:**
- **Validation:** Done before deployment (on static test sets).
- **Monitoring:** Done after deployment (on live, dynamic traffic).

## Drift & Observability

### 4. How do you distinguish between Data Drift and Concept Drift?
**Answer:**
- **Data Drift (Covariate Shift):** The distribution of input features $P(X)$ changes (e.g., users are now older).
- **Concept Drift:** The relationship between features and target $P(Y|X)$ changes (e.g., the definition of "spam" evolves).
- **Fix:** Concept drift almost always requires retraining. Data drift might be handled by re-weighting samples or normalization.

### 5. What are specialized Monitoring Tools for ML?
**Answer:**
- **Prometheus/Grafana:** For system metrics (latency, memory).
- **Whylogs / Evidently AI / Great Expectations:** For data quality and drift detection.
- **Weights & Biases / MLflow:** For experiment tracking and model registry.

### 6. Explain "Prediction Latency SLOs" (Service Level Objectives).
**Answer:**
A commitment to a specific performance level (e.g., "99% of predictions will return in <200ms"). If exceeded, it triggers an alert. As a Senior MLE, you must balance model complexity with these SLOs.
