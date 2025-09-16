# Numerical Optimization Methods

## Overview
This repository implements and compares two Newton-type methods for unconstrained nonlinear optimization:

- **Modified Newton Method**  
  Uses exact or finite-difference derivatives. If the Hessian is not positive definite, a correction matrix `Bk` is built to guarantee descent direction. Line search is performed with Armijo condition and backtracking.

- **Truncated Newton Method**  
  Relies on Hessian–vector products rather than explicitly forming the Hessian. The search direction is computed by a **truncated Conjugate Gradient (CG)** solver, which may stop early due to negative curvature, tolerance, or time limits. Line search is handled with Armijo condition and backtracking.

Both methods are tested on benchmark functions (Rosenbrock, Powell, Broyden) and support exact or approximated derivatives, dense or sparse structures, and adaptive finite-differences.

---

## Project Structure

```
Numerical/
│
├── main_modified.py            # Entry point for the Modified Newton method
├── main_truncated.py           # Entry point for the Truncated Newton method
├── Test.py                     # Experiment orchestration, logging, W&B integration
├── Testers.py                  # Test configurations and parameter grids
│
├── Tools/
│   ├── Functions.py            # Benchmark functions (Rosenbrock, Powell, etc.)
│   ├── Derivatives.py          # Exact and approximate derivatives, Hessian-vector products
│   ├── Linesearch.py           # Armijo backtracking line search
│   ├── Conditions.py           # Stopping criteria, PD checks, perturbation generators
│
├── Modified_Newton_Method/
│   ├── ModifiedNewtonMethod.py # Implementation of the Modified Newton algorithm
│   ├── SolverInstruments.py    # Routines for matrix corrections and incomplete Cholesky
│
├── Truncated_Newton_Method/
│   ├── TruncatedNewtonMethod.py # Implementation of the Truncated Newton algorithm
│   ├── TSolverInstruments.py    # Conjugate Gradient solver for inner iterations
│
└── results/                     # CSV logs, experiment outputs, optional W&B tracking
```

---

## Features

- **Derivative Options**
  - Exact gradient and Hessian for test functions
  - Finite difference approximations (forward, backward, central)
  - Adaptive finite differences
  - Sparse approximations with graph coloring

- **Search Direction**
  - Modified Newton: direct solve with Cholesky or iterative correction if Hessian not SPD
  - Truncated Newton: Hessian–vector products and truncated CG (with optional preconditioning)

- **Line Search**
  - Armijo condition with backtracking

- **Stopping Criteria**
  - Gradient norm threshold
  - Maximum iterations
  - Inner CG tolerances
  - Time limit for truncated solver

- **Logging**
  - Iteration history (`xk`, `f(xk)`, gradient norms)
  - Backtracking sequence
  - Inner iterations
  - Success/failure flags
  - Export to CSV
  - [Optional] Weights & Biases integration for experiment tracking

---

## How to Run

### 1. Setup Environment
```bash
python3 -m venv <path_to_venv>
source <path_to_venv>/bin/activate
pip install -r requirements.txt
```

### 2. Run Modified
```bash
python main_modified.py --function rosenbrock --derivatives exact --max_iters 500
```

### 3. Run Truncated Newton
```bash
python main_truncated.py --function extended_powell --derivatives finite_differences --inner_tol 1e-4
```

### 4. Run Full Experiment Suite
```bash
python Test.py
```

---

## Results
Numerical logs are stored in results/ as .csv files, containing:
	•	Iteration count
	•	Gradient norms
	•	Inner CG iterations
	•	Backtracking steps
	•	Success flags
  •	Optional Weights & Biases (W&B) logging provides interactive experiment dashboards.

---

## Known Issues/TODO
•	Linesearch.Backtracking: may return alphak even if Armijo not satisfied when bt == btmax. Needs post-check and safe fallback.
•	Preconditioning: incomplete Cholesky may fail if matrix not SPD; add safe fallback to non-preconditioned CG.
•	Large dimensions: avoid densifying sparse Hessians in compute_gradient_hessian.
•	Seeds: ensure all seeds used in Test.py are also saved in CSV output for reproducibility.

