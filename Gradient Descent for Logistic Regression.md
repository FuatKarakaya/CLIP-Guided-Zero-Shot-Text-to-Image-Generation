# Gradient Descent for Logistic Regression (Step by Step)

> Your notes derive **how to update the weights** to minimize cross-entropy loss. Let's break it into digestible pieces.

---

## 🎯 The Big Picture

We have parameters to learn: a **bias** $w_0$ and **weights** $w_1, w_2, \ldots, w_n$.

The gradient descent update rule says:

$$w^{(t+1)} = w^{(t)} - \alpha \cdot \frac{\partial \mathcal{L}}{\partial w}$$

This is a **template** — we apply it to each parameter separately:

| Parameter | Its Update Rule |
|-----------|-----------------|
| $w_0$ | $w_0 \leftarrow w_0 - \alpha \cdot \frac{\partial \mathcal{L}}{\partial w_0}$ |
| $w_1$ | $w_1 \leftarrow w_1 - \alpha \cdot \frac{\partial \mathcal{L}}{\partial w_1}$ |
| $w_j$ | $w_j \leftarrow w_j - \alpha \cdot \frac{\partial \mathcal{L}}{\partial w_j}$ |

**Our job**: Find each of these gradients.

---

## 🔗 The Chain Rule: One Formula, Applied to Each Parameter

To find $\frac{\partial \mathcal{L}}{\partial w}$ for ANY parameter, we use the chain rule:

$$\frac{\partial \mathcal{L}}{\partial w} = \frac{\partial \mathcal{L}}{\partial \hat{y}} \cdot \frac{\partial \hat{y}}{\partial s} \cdot \frac{\partial s}{\partial w}$$

Think of it as following the path from $w$ to $\mathcal{L}$:

```
w  →  s  →  ŷ  →  L

w affects s (the linear score)
s affects ŷ (through sigmoid)
ŷ affects L (through cross-entropy)
```

Let's compute each piece:

| Piece | What it represents | Result |
|-------|-------------------|--------|
| $\frac{\partial \mathcal{L}}{\partial \hat{y}}$ | How loss changes with prediction | $\frac{\hat{y} - y}{\hat{y}(1-\hat{y})}$ |
| $\frac{\partial \hat{y}}{\partial s}$ | How prediction changes with score (sigmoid derivative) | $\hat{y}(1-\hat{y})$ |
| $\frac{\partial s}{\partial w}$ | How score changes with the weight | **Depends on which $w$!** |

---

## ⚡ The Key Insight: Only the Last Piece Changes!

The first two pieces are the **same** for every parameter. Only $\frac{\partial s}{\partial w}$ depends on which parameter we're differentiating.

Remember what $s$ looks like:

$$s = w_0 \cdot 1 + w_1 \cdot x_1 + w_2 \cdot x_2 + \cdots + w_n \cdot x_n$$

So:

| If we differentiate w.r.t. | $\frac{\partial s}{\partial w} = $ | Why? |
|---------------------------|-----------------------------------|------|
| $w_0$ (bias) | $1$ | $w_0$ is multiplied by 1 |
| $w_1$ | $x_1$ | $w_1$ is multiplied by $x_1$ |
| $w_j$ | $x_j$ | $w_j$ is multiplied by $x_j$ |

---

## 🧮 Putting It Together

### For the bias $w_0$:

$$\frac{\partial \mathcal{L}}{\partial w_0} = \underbrace{\frac{\hat{y} - y}{\hat{y}(1-\hat{y})}}_{\text{piece 1}} \cdot \underbrace{\hat{y}(1-\hat{y})}_{\text{piece 2}} \cdot \underbrace{1}_{\text{piece 3}}$$

The $\hat{y}(1-\hat{y})$ cancels:

$$\boxed{\frac{\partial \mathcal{L}}{\partial w_0} = \hat{y} - y}$$

### For any weight $w_j$:

$$\frac{\partial \mathcal{L}}{\partial w_j} = \underbrace{\frac{\hat{y} - y}{\hat{y}(1-\hat{y})}}_{\text{piece 1}} \cdot \underbrace{\hat{y}(1-\hat{y})}_{\text{piece 2}} \cdot \underbrace{x_j}_{\text{piece 3}}$$

The $\hat{y}(1-\hat{y})$ cancels:

$$\boxed{\frac{\partial \mathcal{L}}{\partial w_j} = (\hat{y} - y) \cdot x_j}$$

---

## 🎨 Visual Summary

The chain rule structure is **identical** for all parameters:

$$\frac{\partial \mathcal{L}}{\partial w} = \underbrace{\frac{\partial \mathcal{L}}{\partial \hat{y}}}_{\text{same}} \cdot \underbrace{\frac{\partial \hat{y}}{\partial s}}_{\text{same}} \cdot \underbrace{\frac{\partial s}{\partial w}}_{\text{THIS changes}}$$

After cancellation:

| Parameter | Gradient = (error) × (what?) |
|-----------|------------------------------|
| Bias $w_0$ | $(\hat{y} - y) \times 1$ |
| Weight $w_1$ | $(\hat{y} - y) \times x_1$ |
| Weight $w_2$ | $(\hat{y} - y) \times x_2$ |
| Weight $w_j$ | $(\hat{y} - y) \times x_j$ |

**The pattern**: Gradient = (prediction error) × (whatever multiplies that weight in $s$)

---

## 📊 For Multiple Data Points

Sum over all data points $i = 1, 2, \ldots, N$:

$$\frac{\partial \mathcal{L}}{\partial w_0} = \sum_{i=1}^{N} (\hat{y}^{(i)} - y^{(i)})$$

$$\frac{\partial \mathcal{L}}{\partial w_j} = \sum_{i=1}^{N} (\hat{y}^{(i)} - y^{(i)}) \cdot x_j^{(i)}$$

---

## ✅ Final Update Rules

| Parameter | Update Rule |
|-----------|-------------|
| Bias $w_0$ | $w_0 \leftarrow w_0 - \alpha \sum_i(\hat{y}^{(i)} - y^{(i)})$ |
| Weights $w_j$ | $w_j \leftarrow w_j - \alpha \sum_i(\hat{y}^{(i)} - y^{(i)}) \cdot x_j^{(i)}$ |

---

## 💡 Intuition Check

**Q: Why does the weight update include $x_j$ but the bias doesn't?**

**A:** Because the bias doesn't multiply any input! Look at $s$:

$$s = \underbrace{w_0}_{\text{no input}} + \underbrace{w_1 x_1}_{\text{multiplied by } x_1} + \underbrace{w_2 x_2}_{\text{multiplied by } x_2} + \cdots$$

- $w_j$ gets updated proportionally to $x_j$ because a larger $x_j$ means $w_j$ had more influence on the prediction
- $w_0$ is just a constant shift — it affects all predictions equally regardless of input

---

## 🔢 Concrete Example

One data point: $\mathbf{x} = [x_1, x_2] = [3, 5]$, with $y = 1$ and $\hat{y} = 0.4$

Error $= \hat{y} - y = 0.4 - 1 = -0.6$ (we under-predicted)

With $\alpha = 0.1$:

| Parameter | Gradient | Update |
|-----------|----------|--------|
| $w_0$ | $-0.6 \times 1 = -0.6$ | $+0.06$ (increases) |
| $w_1$ | $-0.6 \times 3 = -1.8$ | $+0.18$ (increases more, since $x_1=3$) |
| $w_2$ | $-0.6 \times 5 = -3.0$ | $+0.30$ (increases most, since $x_2=5$) |

$w_2$ gets the biggest update because $x_2$ was largest — it had the most influence on the error!
