# Technical Study Guide: Senior DS (Computer Vision & Agentic AI)

## 1. Traditional Computer Vision (OpenCV)
- **Image Processing:** Kernels, Blurring, Sharpening, and Morphological operations.
- **Feature Matching:** SIFT, SURF, ORB, and RANSAC for robust matching.
- **Geometry:** Camera calibration, Epipolar geometry, and Homographies.
- **Tracking:** Kalman Filters and Optical Flow (Lucas-Kanade).

## 2. Deep Learning for Vision
- **Detection:** Faster R-CNN, YOLO (v8/v10), and SSD.
- **Segmentation:** UNet, Mask R-CNN, and Segment Anything Model (SAM).
- **Embeddings:** Contrastive learning (Triplets) and self-supervised learning (MoCo, DINO).
- **Optimization:** TensorRT, ONNX Runtime, and pruning/quantization strategies.

## 3. Agentic AI & LLMs
- **Orchestration:** LangChain (Chains, Agents, Tools) and AutoGen (Multi-agent conversations).
- **Reasoning:** ReAct (Reasoning and Acting) and Chain-of-Thought (CoT) patterns.
- **Memory:** Managing conversational state and long-term storage for agents.
- **Tool Calling:** Building custom tools for vision models (e.g., an agent calling an OCR tool).

## 4. Vision-Language Models (VLMs)
- **Architecture:** CLIP (Contrastive Language-Image Pretraining), BLIP, and Flamingo.
- **Multimodal RAG:** Using visual embeddings for retrieval-augmented generation.
- **Image Captioning & VQA:** Training and fine-tuning models for visual question answering.

## 5. MLOps & Deployment
- **Lifecycle:** MLflow for tracking experiments and Azure ML for registry/deployment.
- **CI/CD for ML:** Automating model testing and deployment pipelines.
- **Monitoring:** Detecting concept drift and performance degradation in production vision systems.

## 6. Feature Engineering
- **Handcrafted:** HOG (Histogram of Oriented Gradients), LBP (Local Binary Patterns), and Gabor filters.
- **Representation:** Dimensionality reduction (PCA, t-SNE) for visual feature spaces.

## Resources to Review
- [PyTorch Computer Vision Tutorials](https://pytorch.org/tutorials/intermediate/torchvision_tutorial.html)
- [LangChain: Agent Concepts](https://python.langchain.com/docs/modules/agents/)
- [OpenCV Documentation: Feature Detection](https://docs.opencv.org/4.x/db/d27/tutorial_py_table_of_contents_feature_2d.html)
- [The Illustrated CLIP (Multimodal AI)](https://jalammar.github.io/illustrated-clip/)
- [ONNX Model Optimization Guide](https://onnxruntime.ai/docs/performance/model-optimizations/index.html)
