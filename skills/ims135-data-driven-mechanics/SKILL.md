---
name: ims135-data-driven-mechanics
description: >-
  Expert guide, workflows, templates, and exam solutions for Chalmers course
  IMS135 (Machine Learning and Data-Driven Modelling in Mechanics).
  Trigger whenever working on Physics-Informed Neural Networks (PINNs),
  Singular Value Decomposition (SVD) Model Reduction (ROM), polynomial
  curve fitting, K-fold cross-validation, nonlinear optimization (L-BFGS, SGD, Adam),
  SINDy (Sparse Identification of Nonlinear Dynamics), or Chalmers IMS135 exam problems.
---

# IMS135: Data-Driven Mechanics & Machine Learning

This skill encapsulates the complete methodology, theoretical principles, PyTorch/NumPy templates, and past exam patterns for **Chalmers IMS135 (Machine Learning and Data-Driven Modelling in Mechanics)**, examined by Professor Magnus Ekh.

---

## The 5 Core Exam Pillars

Whenever approaching an IMS135 task or exam problem, classify it into one of the 5 Pillars:

1. **Pillar 1: Physics-Informed Neural Networks (PINNs) [5 Points]**
   - Forward PINNs (predicting fields $u(x)$, $T(x)$, $w(x)$ with boundary conditions)
   - Inverse PINNs (discovering unknown material parameters $k(x)$, $EA(x)$ from known fields)
   - Higher-Order / Coupled PINNs (e.g., 4th-order Euler-Bernoulli beam deflection $w(x)$ & load $q(x)$)
2. **Pillar 2: SVD & Reduced Order Modeling (ROM) [5 Points]**
   - Snapshot matrix decomposition: $X = U \Sigma V^T$
   - Low-rank truncation at rank $r$: $X_r = U_r \Sigma_r V_r^T$
   - Cumulative energy capture and relative Frobenius/L2 reconstruction error
   - Storage savings formula: $r(m + n + 1)$ vs $m \times n$
3. **Pillar 3: Curve Fitting, Model Selection & Cross-Validation [4–5 Points]**
   - Train / Validation / Test split with $K$-fold cross-validation (`train_test_and_k_splits`)
   - Polynomial degree selection ($d = 1, \dots, 5$) minimizing validation MSE
   - Log-linearized power-law fitting via pseudo-inverse: $y = k_1 x^{k_2} \implies \ln(y) = \ln(k_1) + k_2 \ln(x)$
   - Explicit neural network forward pass & gradient calculations by hand
4. **Pillar 4: Optimization Algorithms & Loss Landscapes [3–4 Points]**
   - 1st-order: Steepest Descent, SGD, Momentum, Adam
   - 2nd-order / Quasi-Newton: L-BFGS with line search
   - Learning rate ($\alpha$) sensitivity, overshooting, oscillations, and divergence
   - Rosenbrock banana-valley benchmark: Why SGD oscillates across steep walls while L-BFGS follows the valley floor
5. **Pillar 5: SINDy & Decision Trees [3–5 Points]**
   - SINDy: Sparse identification of nonlinear dynamics using library $\Theta(X)$ and LASSO/thresholded regression
   - Decision Trees & Random Forests for mechanical response classification and regression

---

## Pillar 1: PINN Mechanics & Implementation Rules

### Essential Rules & Gotchas:
- **macOS OpenMP Bug:** Always insert at top of script:
  ```python
  import os
  os.environ["KMP_DUPLICATE_LIB_OK"] = "TRUE"
  ```
- **Grid Gradient Tracking:** The 1D grid must be created with `requires_grad=True` and reshaped to `(N, 1)`:
  ```python
  x = torch.linspace(0.0, L, N, requires_grad=True).view(N, 1)
  ```
- **Smooth Activation Functions:** Always use `nn.Tanh()` (never `ReLU`, because second derivative of ReLU is identically zero).
- **Linear Output Layer:** Output layer must be linear (no activation) so physical fields can take any real value.
- **Autograd Derivative Operator:**
  ```python
  from torch.autograd import grad
  def get_derivative(y, x, n):
      if n == 0:
          return y
      dy_dx = grad(y, x, torch.ones(x.size()[0], 1), create_graph=True, retain_graph=True)[0]
      return get_derivative(dy_dx, x, n - 1)
  ```
- **Forward vs. Inverse PINN Loss Strategy:**
  - **Forward PINN:** Network predicts field $u(x)$. Enforce PDE residual + high-weight boundary conditions ($100 \times \text{loss}_{\text{bc}}$).
  - **Inverse PINN:** Physical field $T(x)$ is already known analytically. Network predicts unknown material property $k(x)$. Boundary conditions are already satisfied by $T(x)$, so loss is purely the PDE residual.
- **L-BFGS Training Loop with `closure_wrapper`:**
  `optimizer.step()` expects a function returning a single scalar tensor, so wrap the multi-output closure:
  ```python
  for epoch in range(max_epochs):
      def closure_wrapper():
          loss, loss_de = closure(x)
          loss_history[epoch] = loss_de.item()
          return loss
      optimizer.step(closure_wrapper)
  ```

---

## Pillar 2: SVD & Model Reduction Rules

### Core Equations:
1. **SVD Computation:**
   ```python
   U, S, Vt = np.linalg.svd(X, full_matrices=False)
   ```
2. **Rank-$r$ Reconstruction:**
   ```python
   X_r = U[:, :r] @ np.diag(S[:r]) @ Vt[:r, :]
   ```
3. **Cumulative Energy / Variance:**
   ```python
   energy = np.cumsum(S**2) / np.sum(S**2)
   ```
4. **Relative L2 / Frobenius Reconstruction Error:**
   ```python
   rel_error = np.linalg.norm(X - X_r, 'fro') / np.linalg.norm(X, 'fro')
   ```
5. **Exact Storage Savings Formula:**
   - Original full matrix storage: $m \times n$
   - Reduced model storage (rank $r$): $r \cdot (m + n + 1)$
     - $U_r$: $m \times r$ floats
     - $\Sigma_r$: $r$ floats
     - $V_r^T$: $r \times n$ floats
   - Storage savings: $\text{Savings} = 1 - \frac{r(m + n + 1)}{m \cdot n}$

---

## Pillar 3: Cross-Validation & Curve Fitting Rules

### 1. `train_test_and_k_splits` Workflow:
Copied from Cell 23 of `python_help_notebook.ipynb`:
1. Hold out 10% test set (`test_frac=0.10`) with fixed random seed.
2. Generate $K=4$ or $K=5$ random validation splits on the remaining 90% (`val_frac=0.20`).
3. For each candidate model (e.g. polynomial degree $d \in [1, 2, 3, 4, 5]$):
   - Fit on train split: `p = np.polyfit(x_train, y_train, deg=d)`
   - Evaluate MSE on train and validation splits:
     $$\text{MSE} = \frac{1}{N} \sum (y - \hat{y})^2$$
4. Select degree $d^*$ with minimum average validation MSE.
5. Retrain model on all 90% non-test data using $d^*$, and evaluate final generalization error on the 10% test set.

### 2. Log-Linearized Power Law via Pseudo-Inverse:
For Stefan-Boltzmann radiation, fluid power laws, or constitutive power laws:
$$y = k_1 x^{k_2} \implies \ln(y) = \ln(k_1) + k_2 \ln(x)$$
Let $Y = \ln(y)$, $X = \ln(x)$, $c_0 = \ln(k_1)$, $c_1 = k_2$:
$$\begin{bmatrix} 1 & X_1 \\ \vdots & \vdots \\ 1 & X_N \end{bmatrix} \begin{bmatrix} c_0 \\ c_1 \end{bmatrix} = \begin{bmatrix} Y_1 \\ \vdots \\ Y_N \end{bmatrix} \implies \mathbf{c} = (A^T A)^{-1} A^T Y$$
Then recover physical constants:
$$k_1 = \exp(c_0), \quad k_2 = c_1$$

---

## Pillar 4: Optimization Rules & Comparisons

### 1. First-Order vs. Second-Order Comparison:
- **SGD:** 1st-order gradients only. Highly sensitive to learning rate $\alpha$. Oscillates perpendicular to narrow valleys (Rosenbrock) because steep walls dominate gradient direction.
- **Adam:** Combines Momentum (moving average of gradients) and RMSProp (moving average of squared gradients). Adapts step sizes per parameter.
- **L-BFGS:** Quasi-Newton 2nd-order. Approximates inverse Hessian from recent gradient changes. Includes Wolfe line search. Converges in 15–30 iterations on smooth physical objective functions.

### 2. Learning Rate Dynamics:
- $\alpha$ too small ($< 10^{-4}$): extremely slow convergence, gets stuck in flat plateaus.
- $\alpha$ optimal ($\approx 10^{-2}$): steady, monotonic decrease.
- $\alpha$ too large ($> 1.0$): overshoots valley minimum, oscillates wildly, explodes to `NaN` / infinity.

---

## Pillar 5: SINDy (Sparse Identification of Nonlinear Dynamics)

Given time series data $X$ and derivatives $\dot{X}$:
1. Construct feature library $\Theta(X)$ containing candidate terms:
   $$\Theta(X) = [1, X, X^2, X^3, \sin(X), \dots]$$
2. Solve linear system $\dot{X} = \Theta(X) \Xi$.
3. Apply Sequential Thresholded Ridge Regression (or LASSO L1 penalty) to eliminate small coefficients below threshold $\lambda$:
   $$\Xi[|\Xi| < \lambda] = 0$$
4. Re-solve on remaining active terms to identify the exact analytical ODE/PDE.

---

## Detailed References
For expanded step-by-step code and mathematical formulas, see:
- [pinn_templates.md](./references/pinn_templates.md)
- [svd_model_reduction.md](./references/svd_model_reduction.md)
- [cross_validation_and_fitting.md](./references/cross_validation_and_fitting.md)
- [optimization_guide.md](./references/optimization_guide.md)
