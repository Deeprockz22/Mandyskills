# IMS135 SVD & Reduced Order Modeling (ROM) Reference

## 1. Complete Workflow
```python
import numpy as np
import scipy.io as sio

# --- 1. Load Data ---
# Shape of X: (m, n) where m = spatial degrees of freedom, n = time steps
data = sio.loadmat('temperature_field_240820.mat')
X = data['T'] # e.g. 4646 x 1000
m, n = X.shape

# --- 2. Compute Economy SVD ---
U, S, Vt = np.linalg.svd(X, full_matrices=False)

# --- 3. Determine Rank r by Energy Threshold ---
cum_energy = np.cumsum(S**2) / np.sum(S**2)
r = np.argmax(cum_energy >= 0.99) + 1  # 99% energy capture

# --- 4. Reconstruct Truncated Field ---
Ur = U[:, :r]
Sr = np.diag(S[:r])
Vtr = Vt[:r, :]
X_r = Ur @ Sr @ Vtr

# --- 5. Relative L2 / Frobenius Error ---
rel_error = np.linalg.norm(X - X_r, 'fro') / np.linalg.norm(X, 'fro')
print(f"Rank: {r}, Energy: {cum_energy[r-1]*100:.2f}%, Rel Error: {rel_error*100:.2f}%")

# --- 6. Storage Savings ---
orig_floats = m * n
rom_floats = r * (m + n + 1)
savings = (1.0 - rom_floats / orig_floats) * 100.0
print(f"Storage Savings: {savings:.2f}% (Saved {orig_floats - rom_floats} floats)")
```

## 2. Key Exam Formulas
- **Energy of singular values:** $\sigma_i^2$
- **Low-rank error theorem (Eckart-Young):**
  $$\|X - X_r\|_F^2 = \sum_{i=r+1}^{\min(m,n)} \sigma_i^2$$
- **Storage size:**
  $$U_r \in \mathbb{R}^{m \times r}, \quad \Sigma_r \in \mathbb{R}^{r}, \quad V_r^T \in \mathbb{R}^{r \times n} \implies r(m + n + 1)$$
