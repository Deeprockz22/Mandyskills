# IMS135 Optimization Algorithms Reference

## 1. Comparing SGD vs. L-BFGS on 2D Functions
```python
import torch

def rosenbrock(x, a=2.0, b=5.0):
    return (a - x[0])**2 + b * (x[1] - x[0]**2)**2

# --- Method A: SGD ---
x_sgd = torch.tensor([1.0, 3.0], requires_grad=True)
opt_sgd = torch.optim.SGD([x_sgd], lr=0.01)

for step in range(1000):
    opt_sgd.zero_grad()
    loss = rosenbrock(x_sgd)
    loss.backward()
    opt_sgd.step()

# --- Method B: L-BFGS ---
x_lbfgs = torch.tensor([1.0, 3.0], requires_grad=True)
opt_lbfgs = torch.optim.LBFGS([x_lbfgs], lr=1.0, max_iter=100)

def closure():
    opt_lbfgs.zero_grad()
    loss = rosenbrock(x_lbfgs)
    loss.backward()
    return loss

opt_lbfgs.step(closure)
print(f"L-BFGS converged to: {x_lbfgs.detach().numpy()}")
```

## 2. Gradient Descent with Momentum (from Exam 241031)
```python
x = torch.tensor([-1.0, 0.0], requires_grad=True)
alpha = 0.1   # learning rate
beta1 = 0.9   # momentum coefficient
m = torch.tensor([0.0, 0.0])

for iter in range(200):
    loss = objective_function(x)
    loss.backward()

    with torch.no_grad():
        # Momentum update: m^(k) = beta1 * m^(k-1) + (1 - beta1) * grad
        m = beta1 * m + (1.0 - beta1) * x.grad
        # Parameter step: x^(k+1) = x^(k) - alpha * m^(k)
        x -= alpha * m
        x.grad.zero_()
```
