# 04 - Software Engineering for ML

## Testing & Quality

### 1. What are "Model Tests" vs. "Data Tests"?
**Answer:**
- **Data Tests:** Validating input quality (e.g., using **Great Expectations** to check that 'age' is always positive and not null).
- **Model Tests:** 
    - **Unit Tests:** Checking if `model.predict()` returns the correct shape and type.
    - **Integration Tests:** Checking if the inference service correctly calls the feature store.
    - **Invariance Tests:** Checking if slight changes in input (e.g., changing 'Name' in a credit model) change the prediction (useful for fairness).

### 2. How do you implement CI/CD for Machine Learning?
**Answer:**
- **Continuous Integration (CI):** On every PR, run unit tests, data schema validation, and potentially a "smoke test" training run.
- **Continuous Delivery (CD):** Once merged, automatically build the Docker image and deploy to a staging environment.
- **Continuous Training (CT):** A trigger (time or drift-based) that kicks off a training pipeline and updates the model registry.

### 3. What is "Clean Code" in ML?
**Answer:**
- **Modularity:** Separation of data loading, preprocessing, model definition, and training logic.
- **Configuration Management:** Using YAML/JSON for hyperparameters, not hardcoding them.
- **Logging:** Structured logging for both training (MLflow) and production (Logstash/Splunk).

## System Design

### 4. How do you handle "Cold Starts" in production?
**Answer:**
Occurs when a new instance starts up and needs to load a large model (e.g., 10GB) into memory.
- **Provisioned Concurrency:** Keep a minimum number of instances always running.
- **Model Streaming:** Loading the model in chunks.
- **Warmup Requests:** Sending dummy traffic to initialize the model.

### 5. What is the "Circuit Breaker" pattern in ML inference?
**Answer:**
If the model service starts failing or becoming too slow, the circuit breaker "trips" and directs traffic to a simpler, faster fallback model (or a default value) to prevent the entire application from crashing.

### 6. Importance of Code Reviews for ML Engineers?
**Answer:**
Focus on:
- Correctness of the math/logic.
- Efficiency of data processing (avoiding nested loops).
- Proper use of libraries (e.g., vectorized operations in NumPy).
- Documentation of "why" a specific architectural choice was made.
