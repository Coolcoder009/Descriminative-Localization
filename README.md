## Class Activation Mapping (CAM) - Discriminative Localization

This project demonstrates **Class Activation Mapping (CAM)** to enable discriminative localization — letting a CNN not only classify an image but also highlight the regions responsible for the decision.

---

Traditional CNNs give you **what** the class is, but not **where** or **why**. CAM bridges this gap between **prediction** and **interpretation**.

---

### 1. Discriminative Localization
- **Goal**: Highlight which part of the image led to the prediction.
- Traditional CNNs output class labels but give no insight into the spatial regions influencing the decision.
- *Example*: A CNN predicts "cat", but CAM shows the cat’s face contributed most.

---

### 2. Problem Addressed
- CNNs are black boxes — you get a prediction, but no explanation.
- CAM provides interpretability by visualizing regions important for classification.
- *Example*: In medical imaging, showing “where” the tumor is builds trust.

---

### 3. Class Activation Mapping (CAM)
- Localizes class-specific regions using feature maps and classifier weights.
- Equation (intuition):

  \[
  \text{CAM}_c(x, y) = \sum_k w_k^c f_k(x, y)
  \]

  - \( w_k^c \): Weight for class \( c \)
  - \( f_k(x, y) \): Activation at location \( (x, y) \) in feature map \( k \)
-  *Example*: CAM for "golden retriever" highlights the head and ears.

---

### 4. Global Average Pooling (GAP)
- Replaces fully connected (FC) layers to preserve spatial structure.
- GAP takes the average of each feature map → outputs a vector → passed to a linear classifier.
- *Example*: From feature map (512, 7, 7) → GAP → (512,) → Linear → Softmax
- Benefits:
  - Retains spatial info
  - Reduces overfitting
  - Enables CAM computation

---

### 5. Weakly Supervised Localization
-  No need for bounding boxes — only image-level labels.
-  The model learns to localize during classification training.
-  *Example*: Labeled "dog" → after training, CAM shows where the dog is.

---

### 6. Key Structural Changes

| Component        | Traditional CNN | CAM-based CNN       |
|------------------|------------------|----------------------|
| FC Layers        |  Present (e.g., 4096) |  Removed          |
| GAP Layer        |  Absent         |  Inserted before classifier |
| Classifier       | Multi-layer FC   |  Single Linear layer |
| Localization     |  Not possible   |  Enabled via CAM   |

---

##  Outcomes

### Object Localization
- CAM allows drawing bounding boxes around the most activated region.
-  *Example*: A bird’s head can be localized without box annotations.

### Fine-Grained Recognition
- Differentiates visually similar classes using discriminative parts.
-  *Example*: Distinguish "Pug" vs. "Bulldog" using facial features.

---

## 🛠 Implementation Notes

- **Dataset Used**: [Oxford-IIIT Pet Dataset](https://www.robots.ox.ac.uk/~vgg/data/pets/)
  -  Rich in fine-grained categories.
  -  CIFAR-10 (32x32) was too low-res for effective CAM visualization.

- **Model Setup**:
  - Forward hook added after the 4th convolutional block (just before GAP).
  - Feature maps are saved during the forward pass.
  - Class weights from the final linear layer are used to compute CAM.

- **CAM Generation**:
  - Weighted sum of feature maps using the predicted class’s weights.
  - Normalized and upsampled to original image size for visualization.

---


