# Gradient Descent for Logistic Regression (Step by Step)

> Your notes derive **how to update the weights** to minimize cross-entropy loss. Let's break it into digestible pieces.

---

## 🎯 The Big Picture

We want to find the best weights **w** so our model predicts well. The method:

```
w_new = w_old - α × (∂Loss/∂w)
```

Your notes derive that gradient (∂Loss/∂w). It's done in **3 stages**:

1. **Sigmoid derivative** (the first image)
2. **Gradient for bias w₀** (second image, left side)
3. **Gradient for weights wⱼ** (third image)

---

## 🧮 Stage 1: Derivative of Sigmoid

### The Sigmoid Function:

```
g(s) = 1 / (1 + e^(-s))
```

This is your **ŷ** - the predicted probability.

### Goal: Find dg/ds

Your notes show:

```
dg     e^(-s)         1 + e^(-s)        1
── = ────────── = ────────────── - ──────────
ds   (1+e^(-s))²   (1+e^(-s))²    (1+e^(-s))²
```

Let me make this clearer:

```
     1 + e^(-s) - 1        1           1
   = ──────────────── = ────────── - ──────────²
      (1+e^(-s))²       1+e^(-s)    (1+e^(-s))

   = g(s) - g(s)²

   = g(s) × (1 - g(s))
```

### ✨ The Beautiful Result:

```
dg/ds = g(s) × [1 - g(s)]
```

Or in ML notation:

```
dŷ/ds = ŷ × (1 - ŷ)
```

**This is why sigmoid is popular** - its derivative has a super simple form!

---

## 📝 Quick Reference: What Is "s"?

In logistic regression:

```
s = w₀ + w₁x₁ + w₂x₂ + ... + wₙxₙ
```

Or in vector form: **s = w·x + b**

Then: **ŷ = sigmoid(s)**

---

## 🔗 Stage 2: Gradient of Cross-Entropy w.r.t. Bias (w₀)

### Starting Point - Cross Entropy Loss:

```
L = -Σᵢ [yⁱ log(ŷⁱ) + (1-yⁱ) log(1-ŷⁱ)]
```

### Step 2.1: Derivative w.r.t. ŷ

```
∂L       yⁱ    (1-yⁱ)
──── = - ── + ────────
∂ŷⁱ      ŷⁱ    1-ŷⁱ
```

Combine into single fraction:

```
     -yⁱ(1-ŷⁱ) + (1-yⁱ)ŷⁱ      -yⁱ + yⁱŷⁱ + ŷⁱ - yⁱŷⁱ      ŷⁱ - yⁱ
   = ───────────────────── = ────────────────────────── = ─────────
         ŷⁱ(1-ŷⁱ)                   ŷⁱ(1-ŷⁱ)               ŷⁱ(1-ŷⁱ)
```

### Step 2.2: Chain Rule

We need ∂L/∂w₀. Use the chain rule:

```
∂L      ∂L     ∂ŷⁱ    ∂s
──── = ──── × ──── × ────
∂w₀    ∂ŷⁱ     ∂s    ∂w₀
```

We know:
- ∂L/∂ŷⁱ = (ŷⁱ - yⁱ) / [ŷⁱ(1-ŷⁱ)]
- ∂ŷⁱ/∂s = ŷⁱ(1-ŷⁱ)  ← from Stage 1!
- ∂s/∂w₀ = 1  (since s = w₀ + w₁x₁ + ...)

### Step 2.3: Multiply Them Together

```
∂L      (ŷⁱ - yⁱ)
──── = ─────────── × ŷⁱ(1-ŷⁱ) × 1
∂w₀     ŷⁱ(1-ŷⁱ)
```

**The ŷⁱ(1-ŷⁱ) terms cancel!**

```
∂L
──── = ŷⁱ - yⁱ
∂w₀
```

For all data points:

```
∂L
──── = Σᵢ (ŷⁱ - yⁱ)
∂w₀
```

### 🎉 Result for Bias Update:

```
w₀_new = w₀_old - α × Σᵢ(ŷⁱ - yⁱ)
```

---

## 📊 Stage 3: Gradient w.r.t. Weights (wⱼ)

Same logic, but now ∂s/∂wⱼ = xⱼ (not 1):

```
∂L      ∂L     ∂ŷⁱ    ∂s
──── = ──── × ──── × ────
∂wⱼ    ∂ŷⁱ     ∂s    ∂wⱼ

     (ŷⁱ - yⁱ)
   = ─────────── × ŷⁱ(1-ŷⁱ) × xⱼⁱ
      ŷⁱ(1-ŷⁱ)

   = (ŷⁱ - yⁱ) × xⱼⁱ
```

For all data points:

```
∂L
──── = Σᵢ (ŷⁱ - yⁱ) × xⱼⁱ
∂wⱼ
```

### 🎉 Result for Weight Update:

```
wⱼ_new = wⱼ_old - α × Σᵢ(ŷⁱ - yⁱ)xⱼⁱ
```

---

## 🎨 Summary: The Final Update Rules

| Parameter | Gradient | Update Rule |
|-----------|----------|-------------|
| **Bias (w₀)** | Σᵢ(ŷⁱ - yⁱ) | w₀ = w₀ - α × Σ(ŷ - y) |
| **Weights (wⱼ)** | Σᵢ(ŷⁱ - yⁱ)xⱼⁱ | wⱼ = wⱼ - α × Σ(ŷ - y)xⱼ |

### In Vector Form:

```
w = w - α × Xᵀ(ŷ - y)
b = b - α × Σ(ŷ - y)
```

---

## 💡 Why This Is Beautiful

1. **Simple result**: Despite all the calculus, the gradient is just **(prediction - truth) × input**

2. **Intuitive meaning**:
   - If ŷ > y (predicted too high) → gradient is positive → weights decrease
   - If ŷ < y (predicted too low) → gradient is negative → weights increase
   - Bigger error → bigger update

3. **The sigmoid derivative canceled perfectly** - this isn't a coincidence! Cross-entropy + sigmoid are mathematically "made for each other"

---

## 🔢 Concrete Example

Say for one data point:
- **x** = [1, 2, 3]
- **y** = 1 (true label)
- **ŷ** = 0.7 (predicted prob)
- **α** = 0.1 (learning rate)

Error = ŷ - y = 0.7 - 1 = **-0.3**

Updates:
```
Δw₀ = -0.1 × (-0.3) × 1 = +0.03
Δw₁ = -0.1 × (-0.3) × 1 = +0.03
Δw₂ = -0.1 × (-0.3) × 2 = +0.06
Δw₃ = -0.1 × (-0.3) × 3 = +0.09
```

Since we under-predicted (ŷ < y), all weights **increase** to push the prediction higher next time! ✓
