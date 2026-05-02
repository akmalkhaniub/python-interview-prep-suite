# 03 - ML Pipelines & Deployment

## Pipeline Orchestration

### 1. What are the stages of a production ML Pipeline?
**Answer:**
1. **Data Ingestion:** Sourcing from DBs, streams, or files.
2. **Preprocessing:** Cleaning, normalization, and feature engineering.
3. **Training:** Running experiments and hyperparameter tuning.
4. **Evaluation:** Validating against a hold-out set and "Gold Standard" datasets.
5. **Versioning:** Saving the model and the data used to train it.
6. **Deployment:** Serving the model via an API.

### 2. Tools for ML Orchestration?
**Answer:**
- **Kubeflow:** Cloud-native pipeline management on Kubernetes.
- **Airflow:** General-purpose task scheduler often used for data prep.
- **MLflow:** Tracking experiments and managing the model lifecycle.
- **DVC (Data Version Control):** Versioning data and models like Git.

### 3. How do you handle "Data Drift"?
**Answer:**
Monitoring the statistical distribution of input data over time. If the distribution changes significantly from the training data, the model may become inaccurate.
- **Fix:** Automated alerts and re-training pipelines.

## Serving & Scaling

### 4. What is "Model Serving" (TorchServe, TF Serving, ONNX Runtime)?
**Answer:**
Specialized web servers optimized for running ML models. They handle batching, versioning, and hardware acceleration (GPU).

### 5. Difference between "Batch" and "Online" Inference?
**Answer:**
- **Batch:** Processing a large volume of data at once (e.g., generating weekly recommendations).
- **Online:** Real-time inference for a single request (e.g., classifying a single image).

### 6. Importance of "Feature Stores" (Feast, Hopsworks)?
**Answer:**
A centralized repository to store and serve features for both training and inference. It ensures that the same feature logic is used in both environments, preventing "Train-Serve Skew."
