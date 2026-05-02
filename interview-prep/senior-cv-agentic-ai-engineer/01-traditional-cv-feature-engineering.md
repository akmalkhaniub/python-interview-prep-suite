# 01 - Traditional CV & Feature Engineering

## Image Processing & Morphology

### 1. Explain the difference between Dilation and Erosion.
**Answer:**
- **Erosion:** Erodes away the boundaries of foreground objects. It shrinks objects and removes small white noise.
- **Dilation:** Adds pixels to the boundaries of objects. It expands objects and helps join broken parts of an object.
- **Opening:** Erosion followed by Dilation (good for removing noise).
- **Closing:** Dilation followed by Erosion (good for closing small holes inside objects).

### 2. How does a Sobel Filter work?
**Answer:**
It is a discrete differentiation operator used for edge detection. It computes the gradient of the image intensity at each pixel.
- It uses two $3 \times 3$ kernels (one for horizontal, one for vertical) to calculate the approximation of the derivatives.
- The magnitude of the gradient is $G = \sqrt{G_x^2 + G_y^2}$.

### 3. What is Camera Calibration and why is it necessary?
**Answer:**
The process of estimating the intrinsic (focal length, optical center, distortion) and extrinsic (rotation, translation) parameters of a camera.
- **Why:** To correct lens distortion (radial/tangential) and to map 2D image coordinates to 3D world coordinates for tasks like navigation or measurement.

## Feature Extraction & Engineering

### 4. SIFT vs. SURF vs. ORB?
**Answer:**
- **SIFT (Scale-Invariant Feature Transform):** Highly accurate, scale and rotation invariant, but slow and was patented (now free).
- **SURF (Speeded Up Robust Features):** Faster than SIFT, uses box filters, but less robust to rotation.
- **ORB (Oriented FAST and Rotated BRIEF):** A fast, open-source alternative to SIFT/SURF. It uses FAST for keypoints and BRIEF for descriptors. It is very efficient for real-time and edge applications.

### 5. How do you extract texture features manually?
**Answer:**
- **LBP (Local Binary Patterns):** Labels pixels by thresholding the neighborhood and considering the result as a binary number.
- **GLCM (Gray-Level Co-occurrence Matrix):** Calculates how often pairs of pixels with specific values and in a specified spatial relationship occur in an image.
- **Gabor Filters:** Linear filters used for texture analysis, specifically for frequency and orientation representation.

### 6. Why combine handcrafted features with DL embeddings?
**Answer:**
Handcrafted features (color histograms, HOG, LBP) can capture specific domain knowledge or geometric constraints that a DL model might struggle with given limited data. 
- **Example:** In medical imaging or satellite imagery, specific texture patterns or spectral ratios are vital.
- **Technique:** Concatenate the handcrafted feature vector with the output of a CNN's global average pooling layer before the final classification head.
