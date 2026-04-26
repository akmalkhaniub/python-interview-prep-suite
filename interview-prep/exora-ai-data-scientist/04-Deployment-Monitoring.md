# 04: Model Deployment & Monitoring

A Data Scientist Engineer at Exora must bridge the gap between "Model" and "System".

## 1. Deployment Strategies
- **Batch Inference:** Running predictions on a large dataset periodically (e.g., daily recommendations).
- **Real-time Inference:** Low-latency API (Next.js/FastAPI) that returns a prediction for a single request.
- **Edge Deployment:** Running models on mobile/IoT devices (TensorFlow Lite, ONNX).

## 2. Model Monitoring
- **Data Drift:** The input data distribution changes over time (e.g., users' behavior changes).
- **Concept Drift:** The relationship between input and output changes (e.g., what was "fraud" yesterday is "legit" today).
- **Performance Drift:** Accuracy/F1 starts dropping.

## 3. Retraining Strategies
- **Scheduled:** Retrain every week/month.
- **Triggered:** Retrain only when drift exceeds a threshold.
- **Champion-Challenger (A/B Testing):** Running the new model alongside the old one to compare performance before fully switching.

## 4. Documentation & Version Control
- **Git:** Code versioning.
- **DVC (Data Version Control):** Versioning datasets and model files (since Git isn't good for large binaries).
- **MLflow:** Tracking experiments, parameters, and model artifacts.

## 5. Potential Interview Questions
1. **How do you detect Data Drift?**
   - *Answer:* Use statistical tests like **Kolmogorov-Smirnov (KS) test** or **Population Stability Index (PSI)** to compare the training distribution vs. the production distribution.
2. **What is a "Dockerized" model and why is it useful?**
   - *Answer:* It packages the model, dependencies, and environment into a container. This ensures that the model runs exactly the same in production as it did on my local machine (no "it works on my machine" issues).
3. **How do you handle a model that is performing poorly in production but well on the test set?**
   - *Answer:* This is likely due to **Training-Serving Skew** or **Data Leakage**. I would investigate if the production features are calculated differently or if some "future" data was present in the training set.
