# 04 - TensorFlow & Deep Learning

## Neural Network Architectures

### 1. What are Activation Functions? Name a few.
**Answer:**
Functions that introduce non-linearity into the network, allowing it to learn complex patterns.
- **ReLU (Rectified Linear Unit):** Most common. $f(x) = max(0, x)$. Solves vanishing gradient in some cases.
- **Sigmoid:** Maps input to [0, 1]. Good for binary classification output.
- **Softmax:** Maps input to a probability distribution. Good for multi-class classification output.
- **Tanh:** Maps input to [-1, 1].

### 2. What is the Vanishing Gradient problem?
**Answer:**
During backpropagation, gradients are multiplied. If gradients are small (<1), they shrink exponentially as they propagate back to earlier layers, causing weights to stop updating.
- **Fixes:** ReLU activation, Batch Normalization, Residual Connections (ResNet), proper weight initialization (He/Glorot).

### 3. Difference between Batch, Stochastic, and Mini-batch Gradient Descent?
**Answer:**
- **Batch:** Uses entire dataset for one update. Stable but slow and memory-intensive.
- **Stochastic (SGD):** Uses one sample per update. Fast but noisy.
- **Mini-batch:** Uses a small chunk of data (e.g., 32 or 64 samples). Balance between speed and stability.

## TensorFlow Specifics

### 4. What is the Keras Functional API vs. Sequential API?
**Answer:**
- **Sequential:** Simplest. Linear stack of layers. Good for 90% of use cases.
- **Functional:** Allows for complex architectures (multi-input, multi-output, shared layers, skip connections).

### 5. Explain Dropout and how it helps.
**Answer:**
A regularization technique where randomly selected neurons are ignored during training.
- **Effect:** Prevents neurons from co-adapting too much, forcing the network to learn more robust features.

### 6. What are Callbacks in Keras?
**Answer:**
Objects that can perform actions at various stages of training (e.g., at the start/end of an epoch).
- **EarlyStopping:** Stops training when a monitored metric stops improving.
- **ModelCheckpoint:** Saves the best version of the model.
- **ReduceLROnPlateau:** Reduces learning rate when validation loss plateaus.
