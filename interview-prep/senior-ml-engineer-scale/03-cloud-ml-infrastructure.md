# 03 - Cloud ML Infrastructure

## Platforms

### 1. What are the key components of AWS SageMaker?
**Answer:**
- **Ground Truth:** For data labeling.
- **Processing Jobs:** For feature engineering.
- **Training Jobs:** Ephemeral clusters for model training.
- **Endpoints:** For hosting real-time inference.
- **Pipelines:** Orchestration of the entire DAG.

### 2. When would you choose Databricks over SageMaker/Vertex AI?
**Answer:**
- Choose **Databricks** if your workflow is heavily Spark-based, requires intense data engineering, and focuses on "Delta Lakes." It is excellent for collaborative SQL and Python notebooks.
- Choose **SageMaker/Vertex AI** for deep integration with other AWS/GCP services and managed infrastructure for deep learning training/hosting.

### 3. What is "Infrastructure as Code" (IaC) for ML?
**Answer:**
Using tools like **Terraform** or **Pulumi** to define ML infrastructure (S3 buckets, SageMaker instances, IAM roles) in code. This ensures environments are reproducible and version-controlled.

## MLOps Engineering

### 4. How do you handle Model Registry and Versioning?
**Answer:**
Using a Model Registry (like MLflow or SageMaker Model Registry) to track:
- Model artifacts.
- Training code version (git commit).
- Training data version (DVC tag).
- Hyperparameters and metrics.
This allows for easy rollback and auditing.

### 5. Explain "Serverless Inference" (e.g., SageMaker Serverless).
**Answer:**
Inference where you don't manage instances. It scales automatically and you only pay for the time the model is processing requests. 
- **Pro:** Cost-effective for intermittent traffic. 
- **Con:** Cold start latency can be an issue.

### 6. What is "Containerization" in ML?
**Answer:**
Wrapping the model and its dependencies (Python, libraries, drivers) in a **Docker** container. This ensures the model runs identically in development, staging, and production.
