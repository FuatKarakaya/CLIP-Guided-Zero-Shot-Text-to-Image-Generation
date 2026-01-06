# Interval Estimation z Table

## Formula Reference

**Margin of Error:**
$$E = z_{\alpha/2} \times \frac{\sigma}{\sqrt{N}}$$

**Common z-values:**
| Confidence Level | $z_{\alpha/2}$ |
|------------------|----------------|
| $z_{0.05}$         | 1.645          |
| $z_{0.025}$        | 1.96           |
| $z_{0.005}$        | 2.576          |

---

## Problem
Find the range for average height with 90% confidence.

**Given:**
- Sample size: $N = 100$
- Population standard deviation: $\sigma = 10$ cm
- Sample mean: $\bar{x} = 170$ cm
- Confidence level: 90%

## Solution

### Step 1: Find the critical z-value
For 90% confidence: $z_{0.05} = 1.645$

### Step 2: Calculate margin of error

$$E = z_{\alpha/2} \times \frac{\sigma}{\sqrt{N}}$$

$$E = 1.645 \times \frac{10}{\sqrt{100}}$$

$$E = 1.645 \times \frac{10}{10}$$

$$E = 1.645 \times 1 = 1.645 \text{ cm}$$

### Step 3: Construct confidence interval

$$\bar{x} \pm E = 170 \pm 1.645$$

**Confidence Interval:** $(168.36 \text{ cm}, 171.64 \text{ cm})$

## Answer
We are **90% confident** that the true population mean height lies between **168.36 cm** and **171.64 cm**.
