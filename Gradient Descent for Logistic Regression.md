# Gradient Descent for Logistic Regression (Step by Step)

> Your notes derive **how to update the weights** to minimize cross-entropy loss. Let's break it into digestible pieces.

---

## 🎯 The Big Picture

We want to find the best weights $\mathbf{w}$ so our model predicts well. The method:

$$w^{(t+1)} = w^{(t)} - \alpha \cdot \frac{\partial \mathcal{L}}{\partial w}$$

Your notes derive that gradient $\frac{\partial \mathcal{L}}{\partial w}$. It's done in **3 stages**:

1. **Sigmoid derivative** (the first image)
2. **Gradient for bias $w_0$** (second image, left side)
3. **Gradient for weights $w_j$** (third image)

---

## 🧮 Stage 1: Derivative of Sigmoid

### The Sigmoid Function:

$$g(s) = \frac{1}{1 + e^{-s}}$$

This is your $\hat{y}$ — the predicted probability.

### Goal: Find $\frac{dg}{ds}$

Your notes show:

$$\frac{dg}{ds} = \frac{e^{-s}}{(1+e^{-s})^2} = \frac{1 + e^{-s}}{(1+e^{-s})^2} - \frac{1}{(1+e^{-s})^2}$$

Simplifying:

$$= \frac{1}{1+e^{-s}} - \frac{1}{(1+e^{-s})^2} = g(s) - g(s)^2 = g(s) \cdot (1 - g(s))$$

### ✨ The Beautiful Result:

$$\boxed{\frac{dg}{ds} = g(s) \cdot [1 - g(s)]}$$

Or in ML notation:

$$\frac{d\hat{y}}{ds} = \hat{y}(1 - \hat{y})$$

**This is why sigmoid is popular** — its derivative has a super simple form!

---

## 📝 Quick Reference: What Is $s$?

In logistic regression:

$$s = w_0 + w_1 x_1 + w_2 x_2 + \cdots + w_n x_n = \mathbf{w} \cdot \mathbf{x} + b$$

Then: $\hat{y} = \sigma(s)$

---

## 🔗 Stage 2: Gradient of Cross-Entropy w.r.t. Bias ($w_0$)

### Starting Point — Cross Entropy Loss:

$$\mathcal{L} = -\sum_{i} \left[ y^{(i)} \log(\hat{y}^{(i)}) + (1-y^{(i)}) \log(1-\hat{y}^{(i)}) \right]$$

### Step 2.1: Derivative w.r.t. $\hat{y}$

$$\frac{\partial \mathcal{L}}{\partial \hat{y}^{(i)}} = -\frac{y^{(i)}}{\hat{y}^{(i)}} + \frac{1-y^{(i)}}{1-\hat{y}^{(i)}}$$

Combine into single fraction:

$$= \frac{-y^{(i)}(1-\hat{y}^{(i)}) + (1-y^{(i)})\hat{y}^{(i)}}{\hat{y}^{(i)}(1-\hat{y}^{(i)})} = \frac{\hat{y}^{(i)} - y^{(i)}}{\hat{y}^{(i)}(1-\hat{y}^{(i)})}$$

### Step 2.2: Chain Rule

We need $\frac{\partial \mathcal{L}}{\partial w_0}$. Use the chain rule:

$$\frac{\partial \mathcal{L}}{\partial w_0} = \frac{\partial \mathcal{L}}{\partial \hat{y}^{(i)}} \cdot \frac{\partial \hat{y}^{(i)}}{\partial s} \cdot \frac{\partial s}{\partial w_0}$$

We know:
- $\frac{\partial \mathcal{L}}{\partial \hat{y}^{(i)}} = \frac{\hat{y}^{(i)} - y^{(i)}}{\hat{y}^{(i)}(1-\hat{y}^{(i)})}$
- $\frac{\partial \hat{y}^{(i)}}{\partial s} = \hat{y}^{(i)}(1-\hat{y}^{(i)})$ ← from Stage 1!
- $\frac{\partial s}{\partial w_0} = 1$ (since $s = w_0 + w_1 x_1 + \ldots$)

### Step 2.3: Multiply Them Together

$$\frac{\partial \mathcal{L}}{\partial w_0} = \frac{\hat{y}^{(i)} - y^{(i)}}{\hat{y}^{(i)}(1-\hat{y}^{(i)})} \cdot \hat{y}^{(i)}(1-\hat{y}^{(i)}) \cdot 1$$

**The $\hat{y}^{(i)}(1-\hat{y}^{(i)})$ terms cancel!**

$$\frac{\partial \mathcal{L}}{\partial w_0} = \hat{y}^{(i)} - y^{(i)}$$

For all data points:

$$\boxed{\frac{\partial \mathcal{L}}{\partial w_0} = \sum_{i} \left( \hat{y}^{(i)} - y^{(i)} \right)}$$

---

## 📊 Stage 3: Gradient w.r.t. Weights ($w_j$)

Same logic, but now $\frac{\partial s}{\partial w_j} = x_j$ (not 1):

$$\frac{\partial \mathcal{L}}{\partial w_j} = \frac{\partial \mathcal{L}}{\partial \hat{y}^{(i)}} \cdot \frac{\partial \hat{y}^{(i)}}{\partial s} \cdot \frac{\partial s}{\partial w_j}$$

$$= \frac{\hat{y}^{(i)} - y^{(i)}}{\hat{y}^{(i)}(1-\hat{y}^{(i)})} \cdot \hat{y}^{(i)}(1-\hat{y}^{(i)}) \cdot x_j^{(i)} = (\hat{y}^{(i)} - y^{(i)}) \cdot x_j^{(i)}$$

For all data points:

$$\boxed{\frac{\partial \mathcal{L}}{\partial w_j} = \sum_{i} \left( \hat{y}^{(i)} - y^{(i)} \right) x_j^{(i)}}$$

---

## 🎨 Summary: The Final Update Rules

| Parameter | Gradient | Update Rule |
|-----------|----------|-------------|
| **Bias** $w_0$ | $\sum_i(\hat{y}^{(i)} - y^{(i)})$ | $w_0 \leftarrow w_0 - \alpha \sum(\hat{y} - y)$ |
| **Weights** $w_j$ | $\sum_i(\hat{y}^{(i)} - y^{(i)})x_j^{(i)}$ | $w_j \leftarrow w_j - \alpha \sum(\hat{y} - y)x_j$ |

### In Vector Form:

$$\mathbf{w} \leftarrow \mathbf{w} - \alpha \cdot \mathbf{X}^\top (\hat{\mathbf{y}} - \mathbf{y})$$

$$b \leftarrow b - \alpha \cdot \sum(\hat{y} - y)$$

---

## 💡 Why This Is Beautiful

1. **Simple result**: Despite all the calculus, the gradient is just $(\text{prediction} - \text{truth}) \times \text{input}$

2. **Intuitive meaning**:
   - If $\hat{y} > y$ (predicted too high) → gradient is positive → weights decrease
   - If $\hat{y} < y$ (predicted too low) → gradient is negative → weights increase
   - Bigger error → bigger update

3. **The sigmoid derivative canceled perfectly** — this isn't a coincidence! Cross-entropy + sigmoid are mathematically "made for each other"

---

## 🔢 Concrete Example

Say for one data point:
- $\mathbf{x} = [1, 2, 3]$
- $y = 1$ (true label)
- $\hat{y} = 0.7$ (predicted probability)
- $\alpha = 0.1$ (learning rate)

Error $= \hat{y} - y = 0.7 - 1 = -0.3$

Updates:

$$\Delta w_0 = -0.1 \times (-0.3) \times 1 = +0.03$$

$$\Delta w_1 = -0.1 \times (-0.3) \times 1 = +0.03$$

$$\Delta w_2 = -0.1 \times (-0.3) \times 2 = +0.06$$

$$\Delta w_3 = -0.1 \times (-0.3) \times 3 = +0.09$$

Since we under-predicted ($\hat{y} < y$), all weights **increase** to push the prediction higher next time! ✓
