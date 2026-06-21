# IKSol: Hybrid Neural Network and Jacobian-Based Inverse Kinematics

Independent research project (2024–2025) investigating machine learning approaches to inverse kinematics for robotic manipulators.

**Advanced to the Oregon State Science Fair.**

---

## Overview

IKSol is a hybrid inverse kinematics solver for robotic arms.

Given:

- A target end-effector position `(x, y)`
- Three robotic arm segment lengths

the system predicts the joint angles required to reach that position.

Instead of relying entirely on a classical numerical solver or a neural network, IKSol combines both:

1. A neural network generates a fast initial estimate of the joint angles.
2. A Jacobian-based numerical solver refines that estimate to achieve high positional accuracy.

This approach achieves significantly faster convergence while maintaining the precision of classical inverse kinematics methods.

---

## Motivation

Inverse kinematics (IK) is a fundamental robotics problem with applications in:

- Industrial automation
- Surgical robotics
- Prosthetics
- Agriculture
- Autonomous systems

Traditional approaches often involve tradeoffs:

| Method | Strengths | Weaknesses |
|----------|----------|----------|
| Analytical IK | Exact solutions | Difficult for complex manipulators |
| Jacobian/Numerical IK | Accurate | Slow iterative convergence |
| Neural Networks | Fast predictions | Lower precision and poor generalization |

IKSol was developed to combine the strengths of learning-based and numerical approaches while minimizing their weaknesses.

---

## Methodology

### 1. Dataset Generation

A large forward-kinematics dataset was generated programmatically.

For each combination of:

- Joint lengths
- Joint angles

Forward kinematics was used to calculate the resulting end-effector position.

This produced **729,000 unique robotic arm configurations** containing:

- Joint lengths
- Joint angles
- End-effector x-coordinate
- End-effector y-coordinate

---

### 2. Neural Network

The neural network learns an approximate inverse mapping:

```text
(x, y, l1, l2, l3)
        ↓
(θ1, θ2, θ3)
```

Architecture:

```text
Input Layer: 5 neurons
Hidden Layer: 100 neurons (ReLU)
Hidden Layer: 100 neurons (ReLU)
Hidden Layer: 100 neurons (ReLU)
Output Layer: 3 neurons
```

Training performed using:

- TensorFlow / Keras
- Adam optimizer
- Early stopping
- Custom inverse-kinematics loss function

The network rapidly predicts joint angles that are close to a valid solution.

---

### 3. Jacobian Refinement

The neural network output is used as the initial guess for a Jacobian pseudoinverse solver.

The numerical solver:

1. Computes current end-effector position
2. Calculates positional error
3. Builds the Jacobian matrix
4. Uses the Jacobian pseudoinverse to update joint angles
5. Repeats until convergence

This allows the solver to start near the correct solution rather than from a random initialization.

---

## Results

Testing compared three approaches:

- Neural Network only
- Jacobian solver only
- IKSol (Hybrid)

### Performance Improvements

| Metric | Numerical | Neural Network | IKSol |
|----------|----------|----------|----------|
| Initial Position Error | 0.84 units | 0.08 units | 0.08 units |
| Final Position Error | 1×10⁻⁴ units | 0.08 units | 1×10⁻⁴ units |
| Time to Accurate Solution | 21.3 ms | Never reached tolerance | 11.1 ms |

### Key Findings

- 48% faster convergence than a standalone Jacobian solver
- 90% improvement in initial accuracy
- 799% improvement in final accuracy compared to the neural network alone
- Reduced unnecessary joint motion through optimized angle prediction

The hybrid approach consistently reached accurate solutions in fewer iterations than classical methods.

---

## Example Workflow

```text
Target Position
       ↓
Neural Network
       ↓
Initial Angle Estimate
       ↓
Jacobian Solver
       ↓
Refined Joint Angles
       ↓
Accurate End-Effector Position
```

---

## Technologies Used

- Python
- TensorFlow / Keras
- NumPy
- Pandas
- Matplotlib
- Google Colab

---

## Repository Structure

```text
.
├── iksol.ipynb
├── model/
│   └── my_model.keras
├── data/
│   └── generated_fk_dataset.csv
├── graphs/
│   └── evaluation_plots
└── README.md
```

---

## How to Run

Install dependencies:

```bash
pip install tensorflow numpy pandas matplotlib
```

Run the notebook:

```bash
jupyter notebook
```

or execute the Python script:

```bash
python iksol.py
```

Example input:

```python
target = [0.8, 1.1]
joint_lengths = [0.75, 0.5, 0.5]
```

Example output:

```text
Predicted Angles:
θ1 = 60°
θ2 = 170°
θ3 = 165°

Final Position Error:
0.0001 units
```

---

## Future Work

Potential extensions include:

- 3D inverse kinematics
- Higher degree-of-freedom manipulators
- Velocity and acceleration constraints
- Damped least-squares Jacobian methods
- Testing on physical robotic arms
- Real-time deployment on embedded robotics platforms

---

## Awards

Advanced to the Oregon State Science Fair (2025)

---

## Author

**Roushil Satta**

Independent Research Project (2024–2025)

*Combining machine learning and classical robotics techniques to improve robotic motion control.*
