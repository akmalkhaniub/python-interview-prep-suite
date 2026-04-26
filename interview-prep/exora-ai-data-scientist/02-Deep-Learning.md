# 02: Deep Learning (CNN & RNN)

Deep learning is requested for "specific business needs" at Exora AI.

## 1. Convolutional Neural Networks (CNN)
Used for image and grid-like data.
- **Convolution Layers:** Feature extraction (detecting edges, shapes).
- **Pooling Layers:** Dimensionality reduction (Max Pooling).
- **Fully Connected Layers:** Classification at the end of the network.
- **Popular Architectures:** ResNet (Residual connections), VGG, Inception.

## 2. Recurrent Neural Networks (RNN)
Used for sequential data (Time-series, NLP).
- **The Problem:** Vanishing/Exploding gradients.
- **The Solution:** **LSTM** (Long Short-Term Memory) or **GRU** (Gated Recurrent Units) which use gates to preserve state over time.

## 3. Training Deep Networks
- **Activations:** ReLU (Standard), Sigmoid (Binary class), Softmax (Multi-class).
- **Optimizers:** Adam (Standard), SGD (Stochastic Gradient Descent), RMSProp.
- **Dropout:** Randomly "dropping" neurons during training to prevent overfitting.
- **Early Stopping:** Stopping training when validation loss stops decreasing.

## 4. Frameworks: TensorFlow vs. PyTorch
- **TensorFlow:** Static graphs (historically), great for production (TFX, TF Serving).
- **PyTorch:** Dynamic graphs, more "Pythonic", preferred by researchers.
- *Tip:* Exora mentions both; highlight your ability to use the right tool for the job.

## 5. Potential Interview Questions
1. **What is a "Residual Connection" in ResNet?**
   - *Answer:* It allows the gradient to "skip" layers, helping to train very deep networks without the vanishing gradient problem.
2. **When would you use an LSTM over a standard RNN?**
   - *Answer:* Whenever you have long sequences where the "early" context is important. Standard RNNs forget information very quickly; LSTMs have a "cell state" that carries information across the whole sequence.
3. **What is "Transfer Learning" and why is it useful?**
   - *Answer:* It involves taking a model pre-trained on a massive dataset (like ImageNet) and fine-tuning it on a smaller, specific dataset. It saves time and computation while often providing better results.
