# 04 - Production, MLOps & Edge AI

## Deployment & Edge AI

### 1. What are the challenges of Edge AI vs. Cloud AI?
**Answer:**
- **Resources:** Limited CPU/GPU, memory, and power on Edge.
- **Latency:** Edge provides real-time response (no round-trip to cloud).
- **Privacy:** Data stays on-device.
- **Connectivity:** Edge must work offline or with intermittent signal.

### 2. How do you deploy a model on an NVIDIA Jetson?
**Answer:**
- Train in PyTorch/TensorFlow.
- Export to **ONNX**.
- Use **TensorRT** to optimize the model for the Jetson's specific CUDA cores (FP16/INT8 optimization).
- Deploy using a C++ or Python wrapper with the TensorRT engine.

### 3. What is "Model Distillation"?
**Answer:**
Training a smaller "student" model to mimic the behavior (outputs/logits) of a larger, pre-trained "teacher" model. This allows for smaller, faster models that retain much of the accuracy of the heavy model.

## MLOps for Vision

### 4. How do you handle Data Versioning for vision tasks?
**Answer:**
Using tools like **DVC (Data Version Control)** or **LakeFS**. Unlike code, images are large, so we version the *metadata* (hashes/paths) in Git and store the actual blobs in S3/Azure Blob Storage.

### 5. What are specialized metrics for Monitoring Vision Models?
**Answer:**
- **Input Monitoring:** Check for brightness/contrast shifts (histogram analysis) or blurriness.
- **Output Monitoring:** Confidence score distributions, class frequency shifts.
- **Performance:** Inference latency per frame (especially for real-time tracking).

### 6. Explain the "Data Flywheel" in Computer Vision.
**Answer:**
1. Deploy model.
2. Monitor and identify samples where model is uncertain (low confidence).
3. Human-in-the-loop (HITL) labels these "hard" samples.
4. Retrain model with new data.
5. Redeploy.
This continuous loop ensures the model handles edge cases over time.
