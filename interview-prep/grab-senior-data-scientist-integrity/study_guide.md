# Technical Study Guide: Senior Data Scientist (Grab Integrity)

## 1. Computer Vision & OCR Fundamentals

### Traditional OCR vs. Deep Learning OCR
- **Pipelines:** Detection (CRAFT, DBNet) + Recognition (CRNN, Rosetta, Attention-based).
- **Scene Text Detection:** Challenges with perspective, lighting, and occlusion in user-captured photos.
- **Tools:** Tesseract, EasyOCR, PaddleOCR.

### Vision Transformers (ViT)
- **Architecture:** Patch-based processing, self-attention in images.
- **Benefits:** Better global context compared to CNNs for document layout understanding.

## 2. Multimodal & Vision LLMs (mLLMs/vLLMs)

### Architectures
- **CLIP (Contrastive Language-Image Pre-training):** Learning joint representations of text and images.
- **LLaVA / Flamingo:** Connecting Vision Encoders (like ViT-L) to LLM decoders via projection layers.
- **Document AI:** Donut (Document Understanding Transformer), LayoutLMv3.

### Post-training & Fine-tuning
- **Instruction Tuning:** Training mLLMs to follow specific document extraction commands.
- **LoRA (Low-Rank Adaptation):** Efficiently fine-tuning large vision-language models for KYC tasks.
- **Quantization:** Reducing model size (INT8/FP16) for faster inference in Grab's production environment.

## 3. KYC, Fraud, and Identity Verification

### Anti-Spoofing & Liveness Detection
- **Presentation Attack Detection (PAD):** Identifying 2D (printed) vs 3D (real face) attacks.
- **Digital Manipulation:** Detecting deepfakes or edited identity documents.

### Document Verification
- **MRZ (Machine Readable Zone) Parsing:** Extracting data from passports.
- **Cross-validation:** Matching face on ID with a "selfie" liveness check.

## 4. Engineering at Grab Scale

### Big Data & Modeling
- **Spark:** Processing millions of images for batch model training or historical analysis.
- **Inference Optimization:** Using TensorRT or ONNX Runtime for high-throughput CV models.

### MLOps
- **Model Refresh:** How to automate the retraining of models as new fraud patterns emerge.
- **Monitoring:** Tracking "Rejection Rates" vs "False Positives" in the onboarding funnel.

## 5. Potential Interview Questions

### Technical (CV & mLLM)
1. "How would you design a system to extract fields from an ID card that has significant glare?"
2. "Explain how you would fine-tune a Vision LLM for a zero-shot document classification task."
3. "What are the trade-offs between using a dedicated OCR engine vs. an end-to-end Multimodal LLM?"
4. "How do you detect if a user is uploading a photo of a screen instead of a physical ID?"

### Product & Impact
1. "KYC is a bottleneck for user growth. How do you balance model precision with user friction?"
2. "How would you prioritize which new identity document type to support first?"
3. "Describe a time you translated an academic CV paper into a production feature."
