# Deep Dive: Computer Vision & Deep Learning

This role requires balancing high-speed traditional CV with powerful deep learning architectures.

## 1. Traditional vs. Deep Learning Feature Engineering
*   **Traditional:** Using HOG or Gabor filters to detect specific textures or patterns in low-resource environments.
*   **Deep Learning:** Using the intermediate layers of a ResNet or EfficientNet as generic feature extractors.
*   **The Hybrid Approach:** Concatenating handcrafted features (e.g., color histograms) with CNN embeddings to improve classification on specific datasets (common in industrial inspection).

## 2. Advanced Segmentation (SAM & Mask R-CNN)
*   **Mask R-CNN:** The gold standard for instance segmentation—detecting and masking individual objects.
*   **SAM (Segment Anything Model):** Utilizing zero-shot segmentation capabilities for interactive or generalized vision tasks.
*   **Trade-offs:** SAM is powerful but slow; Mask R-CNN is faster for specific, pre-trained classes.

## 3. Object Detection Optimization
*   **YOLO (You Only Look Once):** Achieving real-time performance on edge devices.
*   **Anchor Boxes vs. Anchor-Free:** Understanding the evolution of detection heads (e.g., YOLOv8 anchor-free vs. YOLOv5).
*   **Loss Functions:** GIoU, DIoU, and CIoU—improving bounding box accuracy through better overlap metrics.

## 4. Edge Deployment (ONNX & Quantization)
*   **ONNX:** Converting models from PyTorch/TensorFlow to a unified format for cross-platform execution.
*   **Quantization:** Reducing weights from Float32 to Int8 to speed up inference on mobile/edge chips by 4x.
*   **Pruning:** Removing less important neurons to reduce model size without significant accuracy loss.

## 5. Coding Exercise: OpenCV Filter
```python
import cv2
import numpy as np

# A simple edge detection function combining Gaussian Blur and Canny
def auto_canny(image, sigma=0.33):
    v = np.median(image)
    lower = int(max(0, (1.0 - sigma) * v))
    upper = int(min(255, (1.0 + sigma) * v))
    edged = cv2.Canny(image, lower, upper)
    return edged

# Usage
img = cv2.imread('input.jpg', 0)
blurred = cv2.GaussianBlur(img, (3, 3), 0)
edges = auto_canny(blurred)
```

## Interview Questions
1.  "Explain the difference between Semantic, Instance, and Panoptic segmentation."
2.  "How do you handle occlusion in object tracking using traditional CV methods?"
3.  "Describe your process for optimizing a segmentation model for a real-time surveillance application."
4.  "What are the benefits of using a Vision Transformer (ViT) over a traditional CNN for feature extraction?"
