# IMS135 PINN Templates & Cheat Sheet

## 1. Minimal Working Template: Forward PINN (Axial Bar / Heat)
```python
import os
os.environ["KMP_DUPLICATE_LIB_OK"] = "TRUE"

import torch
import torch.nn as nn
import torch.optim as optim
import numpy as np
import matplotlib.pyplot as plt
from torch.autograd import grad

device = torch.device("cpu")

# --- 1. Grid & Collocation Points ---
L = 1.0
N = 20
x = torch.linspace(0.0, L, N, requires_grad=True).view(N, 1)

# --- 2. Architecture ---
class pinnModel(nn.Module):
    def __init__(self):
        super(pinnModel, self).__init__()
        self.fc1 = nn.Linear(1, 20)
        self.fc2 = nn.Linear(20, 1)
        self.tanh = nn.Tanh()

    def forward(self, x):
        return self.fc2(self.tanh(self.fc1(x)))

model = pinnModel()

# --- 3. Autograd Derivative ---
def get_derivative(y, x, n):
    if n == 0:
        return y
    dy_dx = grad(y, x, torch.ones(x.size()[0], 1, device=device), create_graph=True, retain_graph=True)[0]
    return get_derivative(dy_dx, x, n - 1)

# --- 4. Loss Function ---
def criterion(x, u_pred):
    du_dx = get_derivative(u_pred, x, 1)
    d2u_dx2 = get_derivative(du_dx, x, 1)
    
    # Example PDE: d2u/dx2 + p(x) = 0
    p = torch.sin(np.pi * x / L)
    diff_eq_residual = d2u_dx2 + p
    loss_de = torch.sum(diff_eq_residual ** 2)

    # Boundary conditions: u(0) = 0, u(L) = 1
    loss_bc = (u_pred[0] - 0.0) ** 2 + (u_pred[-1] - 1.0) ** 2
    return loss_de, loss_bc

# --- 5. L-BFGS Training Loop ---
optimizer = optim.LBFGS(model.parameters(), lr=0.01, max_iter=2000, tolerance_change=1e-7)

for epoch in range(100):
    def closure_wrapper():
        optimizer.zero_grad()
        u_pred = model(x)
        loss_de, loss_bc = criterion(x, u_pred)
        loss = loss_de + 100.0 * loss_bc
        loss.backward()
        return loss

    loss = optimizer.step(closure_wrapper)
```

## 2. Minimal Working Template: Inverse PINN (Parameter Discovery)
```python
# T(x) is known analytically or from measurements.
# Network predicts unknown material parameter k(x).
def criterion_inverse(model, x):
    T = T_analytic(x)
    dT_dx = get_derivative(T, x, 1)

    k_pred = model(x)             # Network predicts unknown k(x)
    q = -k_pred * dT_dx           # Fourier's flux
    dq_dx = get_derivative(q, x, 1)

    # PDE residual: -dq/dx + Q = 0
    diff_eq_residual = -dq_dx + 50.0
    loss_de = torch.sum(diff_eq_residual ** 2).view(1)
    return loss_de
```
