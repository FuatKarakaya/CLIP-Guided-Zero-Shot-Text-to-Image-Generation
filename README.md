# SVM Problem: F.R.O.S.T. Magical Snowman Classification

The F.R.O.S.T. (Fictional Research On Snowman Taxonomy) scientists have collected data on magical, talking snowmen. They want to classify snowmen as **friendly (+)** or **evil (-)** based on:
- **x₁**: Number of coal buttons
- **x₂**: Height (in feet)

![Snowman Classification Data](snow.png)

---

## Question A2 (10 points)

**In this SVM, what is the vector w and the constant b?**

### Solution

**Step 1: Identify the data points from the graph**

| Class | Points (buttons, height) | Sum (x₁ + x₂) |
|-------|-------------------------|---------------|
| Evil (−) | (1, 4), (2,3), (1, 5), (3, 3) | 5, 5, 6, 6 |
| Friendly (+) | (4, 6), (5, 6), (4, 7), (6, 7) | 10, 11, 11, 13 |

**Step 2: Identify support vectors**

The support vectors are the points closest to the decision boundary:
- **Negative SVs**: (1, 5) and (3, 3) — both have sum = 6 (maximum among negatives)
- **Positive SV**: (4, 6) — has sum = 10 (minimum among positives)

**Step 3: Set up the canonical SVM equations**

For support vectors, we require $\mathbf{w} \cdot \mathbf{x} + b = \pm 1$:

$$w_1(1) + w_2(5) + b = -1 \quad \text{...(from (1,5))}$$

$$w_1(3) + w_2(3) + b = -1 \quad \text{...(from (3,3))}$$

$$w_1(4) + w_2(6) + b = +1 \quad \text{...(from (4,6))}$$

**Step 4: Solve the system**

From equations (1) and (2):
$$w_1 + 5w_2 = 3w_1 + 3w_2$$
$$2w_2 = 2w_1 \implies w_1 = w_2$$

Let $w_1 = w_2 = c$. Substituting into equation (1):
$$c + 5c + b = -1 \implies 6c + b = -1$$

Substituting into equation (3):
$$4c + 6c + b = 1 \implies 10c + b = 1$$

Subtracting: $4c = 2 \implies c = 0.5$

Therefore: $b = -1 - 6(0.5) = -4$

### Answer

$$\boxed{\mathbf{w} = \begin{bmatrix} 0.5 \\ 0.5 \end{bmatrix} = \begin{bmatrix} 1/2 \\ 1/2 \end{bmatrix}, \quad b = -4}$$

**Verification:**

| Point | $\mathbf{w} \cdot \mathbf{x} + b$ | Expected | ✓ |
|-------|----------------------------------|----------|---|
| (1, 4) | 0.5(1) + 0.5(4) − 4 = −1.5 | ≤ −1 | ✓ |
| (1, 5) | 0.5(1) + 0.5(5) − 4 = −1 | = −1 (SV) | ✓ |
| (3, 3) | 0.5(3) + 0.5(3) − 4 = −1 | = −1 (SV) | ✓ |
| (4, 6) | 0.5(4) + 0.5(6) − 4 = +1 | = +1 (SV) | ✓ |
| (5, 6) | 0.5(5) + 0.5(6) − 4 = +1.5 | ≥ +1 | ✓ |
| (6, 7) | 0.5(6) + 0.5(7) − 4 = +2.5 | ≥ +1 | ✓ |

---

## Question A3 (10 points)

**Suppose the F.R.O.S.T. scientists discover another magical, talking snowman that is five feet tall and has six coal buttons on his chest. What would the α value of this new data point be?**

### Solution

The new snowman has coordinates: $\mathbf{x}_{\text{new}} = (6, 5)$ (6 buttons, 5 feet tall)

**Step 1: Compute the decision function value**

$$\mathbf{w} \cdot \mathbf{x}_{\text{new}} + b = 0.5(6) + 0.5(5) + (-4) = 3 + 2.5 - 4 = 1.5$$

**Step 2: Analyze the result**

Since $\mathbf{w} \cdot \mathbf{x}_{\text{new}} + b = 1.5 > 1$:
- The snowman is classified as **friendly (+)** ✓
- The point lies **outside the margin** (beyond the +1 boundary)

**Step 3: Determine α**

In hard-margin SVM, by the **complementary slackness** (KKT) condition:

$$\alpha_i \left[ y_i(\mathbf{w} \cdot \mathbf{x}_i + b) - 1 \right] = 0$$

This means:
- If a point is **on the margin** ($\mathbf{w} \cdot \mathbf{x} + b = \pm 1$), then $\alpha > 0$ → it's a **support vector**
- If a point is **beyond the margin** ($|\mathbf{w} \cdot \mathbf{x} + b| > 1$), then $\alpha = 0$ → it's **not a support vector**

Since the new point (6, 5) has $\mathbf{w} \cdot \mathbf{x} + b = 1.5 > 1$, it is **not on the margin**.

### Answer

$$\boxed{\alpha = 0}$$

The new snowman (6 buttons, 5 feet tall) would **not** be a support vector because it lies outside the margin boundary. Only points exactly on the margin (where $\mathbf{w} \cdot \mathbf{x} + b = \pm 1$) have non-zero α values. Adding this point to the training set would not change the decision boundary.
