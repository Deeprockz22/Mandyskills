# IMS135 Curve Fitting & K-Fold Cross-Validation Reference

## 1. K-Fold Cross-Validation Script
```python
import numpy as np

def train_test_and_k_splits(data_indices, test_frac=0.10, k=5, val_frac=0.20, seed=1):
    rng = np.random.default_rng(seed)
    n = len(data_indices)
    test_count = int(round(test_frac * n))

    test_idx = rng.choice(n, size=test_count, replace=False)
    test_idx.sort()

    rem_mask = np.ones(n, dtype=bool)
    rem_mask[test_idx] = False
    remaining = np.arange(n)[rem_mask]
    n_rem = len(remaining)
    val_count = int(round(val_frac * n_rem))

    splits = []
    for _ in range(k):
        val_local = rng.choice(n_rem, size=val_count, replace=False)
        val_local.sort()
        val_idx = remaining[val_local]

        train_mask = np.ones(n_rem, dtype=bool)
        train_mask[val_local] = False
        train_idx = remaining[train_mask]
        splits.append((train_idx, val_idx))

    return test_idx, splits

# --- Model Selection Loop Across Polynomial Degrees ---
degrees = [1, 2, 3, 4, 5]
avg_val_errors = []

for d in degrees:
    val_errors = []
    for train_idx, val_idx in splits:
        p = np.polyfit(x[train_idx], y[train_idx], deg=d)
        y_val_pred = np.polyval(p, x[val_idx])
        val_mse = np.mean((y[val_idx] - y_val_pred)**2)
        val_errors.append(val_mse)
    avg_val_errors.append(np.mean(val_errors))

best_degree = degrees[np.argmin(avg_val_errors)]
```

## 2. Log-Linearized Pseudo-Inverse for Power Laws
For physical relationships like $y = k_1 x^{k_2}$:
```python
# Transform to linear form: ln(y) = ln(k1) + k2 * ln(x)
Y = np.log(y)
X_log = np.log(x)

# Construct design matrix A: [1, ln(x)]
A = np.column_stack([np.ones_like(X_log), X_log])

# Solve normal equations: c = (A^T A)^(-1) A^T Y
c = np.linalg.pinv(A) @ Y

k1 = np.exp(c[0])
k2 = c[1]
print(f"Fitted parameters: k1 = {k1:.4f}, k2 = {k2:.4f}")
```
