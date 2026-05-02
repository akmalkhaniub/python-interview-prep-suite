# 02 - Deep Learning for Vision

## Object Detection & Segmentation

### 1. One-stage vs. Two-stage Object Detectors?
**Answer:**
- **Two-stage (e.g., Faster R-CNN):** 
    1. Propose regions (Region Proposal Network).
    2. Classify and refine boxes.
    - *Pros:* More accurate. *Cons:* Slower.
- **One-stage (e.g., YOLO, SSD, RetinaNet):** 
    - Predicts classes and bounding boxes directly in a single pass.
    - *Pros:* Faster (real-time). *Cons:* Historically less accurate for small objects (though YOLOv8+ are very competitive).

### 2. Semantic vs. Instance vs. Panoptic Segmentation?
**Answer:**
- **Semantic:** Classify every pixel into a category (e.g., all "cars" are the same color).
- **Instance:** Detect and segment every individual object (e.g., separate colors for Car 1, Car 2).
- **Panoptic:** Combines both. Assigns a class to every pixel AND an instance ID to "thing" classes (objects).

### 3. What is the IoU (Intersection over Union) metric?
**Answer:**
A metric used to evaluate object detectors. $IoU = Area\ of\ Overlap / Area\ of\ Union$.
- IoU > 0.5 is usually considered a "hit" or TP (True Positive).

## Model Optimization & Deployment

### 4. What is ONNX and why use it?
**Answer:**
**Open Neural Network Exchange.** A standard for representing ML models.
- **Why:** Allows moving models between frameworks (PyTorch -> ONNX -> TensorRT/OpenVINO). It enables hardware-specific optimizations for inference speed without being tied to the training framework.

### 5. Explain Quantization and Pruning.
**Answer:**
- **Quantization:** Reducing the precision of weights (e.g., FP32 to INT8). This reduces model size and speeds up inference on hardware with integer support.
- **Pruning:** Removing redundant or "unimportant" connections (weights) in a network. This makes the model sparser and reduces the number of operations.

### 6. How do you handle small object detection?
**Answer:**
- **Image Tiling:** Split large images into smaller overlapping patches.
- **Feature Pyramids (FPN):** Use multi-scale feature maps to detect objects at different resolutions.
- **Higher Resolution Input:** Simply increasing the input size.
- **Data Augmentation:** Using "Small Object Augmentation" (copy-pasting small objects).
