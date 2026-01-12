# Linear Optimization Problem: A Complete Solution

## The Problem

**Maximize:** $x_1 + 6x_2$

**Subject to:**
- $x_1 \leq 200$
- $x_2 \leq 300$
- $x_1 + x_2 \leq 400$
- $x_1, x_2 \geq 0$ (implicit non-negativity)

---

## Part 1: Understanding Lagrange Multipliers

Before diving into the solution, let's build intuition for **Lagrange multipliers**—a powerful technique for solving constrained optimization problems.

### The Core Idea

Imagine you want to maximize a function $f(x)$, but you're restricted to only certain values of $x$ by a constraint $g(x) \leq b$.

**Without constraints:** Just find where $\nabla f = 0$ (the gradient is zero).

**With constraints:** The optimal point might be:
1. In the interior (constraint inactive) → solve as if unconstrained
2. On the boundary (constraint active) → the constraint "pushes" the solution

### The Lagrangian Function

For a problem with inequality constraints, we form the **Lagrangian**:

$$\mathcal{L}(x, \lambda) = f(x) - \sum_i \lambda_i \cdot g_i(x)$$

Where:
- $f(x)$ is the objective function we want to maximize
- $g_i(x) \leq b_i$ are our constraints (rewritten as $g_i(x) - b_i \leq 0$)
- $\lambda_i \geq 0$ are the **Lagrange multipliers** (one per constraint)

### What Do Lagrange Multipliers Mean?

Each $\lambda_i$ tells you: **"How much would the optimal value improve if I relaxed constraint $i$ by one unit?"**

- $\lambda_i = 0$ → Constraint $i$ is **not active** (slack); the solution doesn't touch this boundary
- $\lambda_i > 0$ → Constraint $i$ is **active** (binding); this constraint is limiting your solution

This is called **complementary slackness**: either the constraint has slack, or the multiplier is positive—never both.

---

## Part 2: Geometric Intuition (The Feasible Region)

Let's visualize our constraints:

| Constraint | Boundary Line | Region |
|------------|---------------|--------|
| $x_1 \leq 200$ | Vertical line at $x_1 = 200$ | Left of line |
| $x_2 \leq 300$ | Horizontal line at $x_2 = 300$ | Below line |
| $x_1 + x_2 \leq 400$ | Diagonal line through $(400, 0)$ and $(0, 400)$ | Below/left of line |

The **feasible region** is the intersection of all these regions—a polygon with vertices at:
- $(0, 0)$
- $(200, 0)$
- $(200, 200)$ — intersection of $x_1 = 200$ and $x_1 + x_2 = 400$
- $(100, 300)$ — intersection of $x_2 = 300$ and $x_1 + x_2 = 400$
- $(0, 300)$

### Finding the Optimal Point Graphically

The objective $x_1 + 6x_2$ has gradient $(1, 6)$, meaning $x_2$ is **6 times more valuable** than $x_1$.

To maximize, we want to go as far as possible in the direction of this gradient while staying in the feasible region. This pushes us toward **high $x_2$ values**.

**Optimal vertex:** $(100, 300)$

**Optimal value:** $1 \cdot 100 + 6 \cdot 300 = 100 + 1800 = \boxed{1900}$

---

## Part 3: The Dual Problem (What Your Class Solution Shows)

> [!IMPORTANT]
> **Why not just set derivatives to zero like in videos?**
> 
> In videos, you often see **equality constraints** (e.g., $g(x) = 0$). For those, you set $\frac{\partial \mathcal{L}}{\partial x} = 0$ and $\frac{\partial \mathcal{L}}{\partial \lambda} = 0$, then solve.
> 
> But our problem has **inequality constraints** ($\leq$). This changes everything because:
> - The optimal point might be **inside** the region (constraint inactive, $\lambda = 0$)
> - The optimal point might be **on the boundary** (constraint active, $\lambda > 0$)
> 
> We don't know in advance which case applies! So we use a different technique: **form the dual problem**.

### Roadmap: What We'll Do

| Step | What We Do | Why |
|------|------------|-----|
| **Step 1** | Write the Lagrangian | Combine objective and constraints into one function |
| **Step 2** | Rearrange by grouping $x_1$ and $x_2$ terms | See what conditions we need on $\lambda$ |
| **Step 3** | Find when max over $x$ is finite | This gives us the dual constraints |
| **Step 4** | Write the dual problem | A minimization over $\lambda$ |
| **Step 5** | Use strong duality | Primal max = Dual min |

---

### Step 1: Setting Up the Primal Problem

Rewrite constraints in standard form ($\leq$ form):

$$\text{Maximize } x_1 + 6x_2$$

**Subject to:**
- $x_1 \leq 200 \quad (1)$
- $x_2 \leq 300 \quad (2)$
- $x_1 + x_2 \leq 400 \quad (3)$

### Step 2: Forming the Lagrangian

Introduce multipliers $\lambda_1, \lambda_2, \lambda_3 \geq 0$ for each constraint:

$$\mathcal{L}(x_1, x_2, \lambda) = (x_1 + 6x_2) - \lambda_1(x_1 - 200) - \lambda_2(x_2 - 300) - \lambda_3(x_1 + x_2 - 400)$$

Rearranging:

$$\mathcal{L} = x_1(1 - \lambda_1 - \lambda_3) + x_2(6 - \lambda_2 - \lambda_3) + 200\lambda_1 + 300\lambda_2 + 400\lambda_3$$

### Step 3: Deriving the Dual Problem (Finding When Max is Finite)

For the Lagrangian to have a finite maximum over $x_1, x_2 \geq 0$, the coefficients of $x_1$ and $x_2$ must be **non-positive** (otherwise we could make $x$ arbitrarily large):

$$1 - \lambda_1 - \lambda_3 \leq 0 \quad \Rightarrow \quad \lambda_1 + \lambda_3 \geq 1$$
$$6 - \lambda_2 - \lambda_3 \leq 0 \quad \Rightarrow \quad \lambda_2 + \lambda_3 \geq 6$$

### Step 4: Writing the Dual Problem

The **dual problem** becomes:

$$\text{Minimize } 200\lambda_1 + 300\lambda_2 + 400\lambda_3$$

**Subject to:**
- $\lambda_1 + \lambda_3 \geq 1$
- $\lambda_2 + \lambda_3 \geq 6$
- $\lambda_1, \lambda_2, \lambda_3 \geq 0$

### Step 5: Strong Duality

Now we have **two optimization problems**:

| | **Primal Problem** | **Dual Problem** |
|---|---|---|
| **Variables** | $x_1, x_2$ (the original decision variables) | $\lambda_1, \lambda_2, \lambda_3$ (the multipliers) |
| **Objective** | Maximize $x_1 + 6x_2$ | Minimize $200\lambda_1 + 300\lambda_2 + 400\lambda_3$ |
| **Optimal Value** | The maximum value of $x_1 + 6x_2$ that satisfies all constraints | The minimum value of $200\lambda_1 + 300\lambda_2 + 400\lambda_3$ that satisfies dual constraints |

**Strong Duality Theorem:** For linear programs, these two optimal values are **equal**:

$$\max(x_1 + 6x_2) = \min(200\lambda_1 + 300\lambda_2 + 400\lambda_3)$$

---

## Part 4: Solving the Dual Problem

Now let's actually **solve** the dual problem to find the optimal value.

### The Dual Problem (Recap)

$$\text{Minimize } 200\lambda_1 + 300\lambda_2 + 400\lambda_3$$

**Subject to:**
- $\lambda_1 + \lambda_3 \geq 1$
- $\lambda_2 + \lambda_3 \geq 6$
- $\lambda_1, \lambda_2, \lambda_3 \geq 0$

### Strategy: Minimize the Objective

We want to make $200\lambda_1 + 300\lambda_2 + 400\lambda_3$ as **small** as possible.

**Key insight:** Looking at the coefficients $(200, 300, 400)$:
- $\lambda_1$ costs 200 per unit
- $\lambda_2$ costs 300 per unit
- $\lambda_3$ costs 400 per unit

So $\lambda_1$ is the "cheapest" variable. But we must satisfy the constraints!

### Trying $\lambda_1 = 0$ (to minimize cost)

If we set $\lambda_1 = 0$, the first constraint becomes:
$$0 + \lambda_3 \geq 1 \quad \Rightarrow \quad \lambda_3 \geq 1$$

To minimize, set $\lambda_3 = 1$ (smallest value that works).

Now the second constraint:
$$\lambda_2 + 1 \geq 6 \quad \Rightarrow \quad \lambda_2 \geq 5$$

To minimize, set $\lambda_2 = 5$.

### The Dual Solution

$$\lambda_1 = 0, \quad \lambda_2 = 5, \quad \lambda_3 = 1$$

**Dual objective value:**
$$200(0) + 300(5) + 400(1) = 0 + 1500 + 400 = \boxed{1900}$$

By **strong duality**, the primal maximum is also **1900**.

---

## Part 5: Recovering the Primal Solution $(x_1, x_2)$

We found the optimal value is 1900, but what are the optimal $x_1$ and $x_2$?

### Using Complementary Slackness

Remember: if $\lambda_i > 0$, then constraint $i$ must be **active** (equality holds).

From our dual solution:
- $\lambda_1 = 0$ → Constraint 1 may or may not be active
- $\lambda_2 = 5 > 0$ → Constraint 2 **must be active**: $x_2 = 300$
- $\lambda_3 = 1 > 0$ → Constraint 3 **must be active**: $x_1 + x_2 = 400$

### Solving for $x_1$ and $x_2$

From the active constraints:
$$x_2 = 300$$
$$x_1 + x_2 = 400 \quad \Rightarrow \quad x_1 + 300 = 400 \quad \Rightarrow \quad x_1 = 100$$

### The Primal Solution

$$x_1 = 100, \quad x_2 = 300$$

**Verification:** $1 \cdot 100 + 6 \cdot 300 = 100 + 1800 = 1900$ ✓

---

## Part 6: Interpreting the Multipliers

The multipliers tell us the **marginal value** of each constraint:

| Multiplier | Value | Interpretation |
|------------|-------|----------------|
| $\lambda_1 = 0$ | 0 | Relaxing $x_1 \leq 200$ gives no benefit (we're not even using all 200!) |
| $\lambda_2 = 5$ | 5 | Each additional unit of $x_2$ allowed would increase objective by 5 |
| $\lambda_3 = 1$ | 1 | Each additional unit in the combined budget would increase objective by 1 |

### Why $\lambda_2 = 5$ Makes Sense

The coefficient of $x_2$ in the objective is 6. If we relax $x_2 \leq 300$ to $x_2 \leq 301$:
- We can set $x_2 = 301$
- But constraint 3 ($x_1 + x_2 \leq 400$) forces $x_1 \leq 99$
- Net change: $+6$ from $x_2$, $-1$ from $x_1$ = **+5**

This confirms $\lambda_2 = 5$!

---

## Summary

| Item | Value |
|------|-------|
| **Optimal Solution** | $x_1 = 100, \; x_2 = 300$ |
| **Optimal Objective** | $1900$ |
| **Active Constraints** | $x_2 \leq 300$ and $x_1 + x_2 \leq 400$ |
| **Lagrange Multipliers** | $\lambda_1 = 0, \; \lambda_2 = 5, \; \lambda_3 = 1$ |

### The Big Picture

1. **Primal** = original problem (maximize profit)
2. **Dual** = "shadow price" problem (minimize the cost of constraints)
3. **Lagrange multipliers** = bridge between the two, telling you which constraints matter
4. **Strong duality** = both problems have the same optimal value
