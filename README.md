# Mandyskills

A curated collection of specialized AI Agent Skills for engineering, mechanics, and design thinking.

---

## Included Skills

### 1. `machine-learning-skill`
Expert workflows, mathematical formulations, and PyTorch/NumPy templates for **Machine Learning and Data-Driven Modelling in Mechanics** (based on Chalmers course IMS135).

- **Physics-Informed Neural Networks (PINNs):** Forward PDEs (elastic bar, heat equation, beam bending), Inverse parameter discovery (unknown thermal conductivity, axial stiffness), and custom boundary condition penalty weighting.
- **Singular Value Decomposition (SVD) Model Reduction (ROM):** Low-rank truncation, cumulative energy criteria, relative Frobenius reconstruction error, and exact memory storage savings calculations.
- **Curve Fitting & K-Fold Cross-Validation:** Train/Val/Test splits via `train_test_and_k_splits`, polynomial model selection, and log-linearized power-law fitting via pseudo-inverse.
- **Optimization Algorithms:** Comparative dynamics of SGD vs. Adam vs. L-BFGS, learning rate sensitivity, and custom momentum implementations.
- **SINDy:** Sparse identification of nonlinear dynamics via candidate feature libraries and thresholded regression.

📁 **Location:** [`skills/machine-learning-skill/`](./skills/machine-learning-skill/)

---

### 2. `jobs-design-lens`
Builds a customized **Steve Jobs Design Persona** tailored to any product, hardware, mobile app, or engineering project.

- Researches core principles from primary sources (Walter Isaacson biography, *Make Something Wonderful*, *Insanely Simple*, *Creative Selection*).
- Formulates project-specific "Design Laws" (simplicity, removing unnecessary elements, craft).
- Provides a Jobs-voice critique checklist and design review framework.

📁 **Location:** [`skills/jobs-design-lens/`](./skills/jobs-design-lens/)

---

## How to Install in Antigravity / Gemini CLI

To install these skills locally in your global Antigravity/Gemini environment:

```bash
# Clone the repository
git clone https://github.com/Deeprockz22/Mandyskills.git

# Copy skills to your global config
cp -r Mandyskills/skills/* ~/.gemini/config/skills/
```

Or copy into a specific workspace's project directory:
```bash
cp -r Mandyskills/skills/* <your-project>/.agents/skills/
```
