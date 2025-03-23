---
layout: default
title: QPs with Interior Point Method
permalink: /qp-ipm/
katex: true
date:   2025-03-23 12:45:34 +0530
---

Consider the inequality-constrained quadratic program (QP) and its solution via the interior point method by introducing slack variables. Slack variables are a handy way to convert inequality constraints into equality constraints, which can simplify the problem's structure and make it more intuitive to handle in some contexts. 

## Original Problem
We start with the QP: <br />
**Objective**: Minimize $f(x)= \frac{1}{2} x^T Q x + c^T x$ <br />
**Constraints**: $Ax \leq b$ <br />
Here, $Q$ is symmetric positive definite matrix, $x$ and $c$ are vectors, $A$ is a matrix, and $b$ is a vector.

## Reformulate with Slack Variables <br />
To handle the inequality $Ax \leq b$, introduce a vector of slack variables $s \geq 0$, where $s$ represents the "slack" or gap between $Ax$ and $b$. This transforms the inequality into an equality: <br />
$Ax+s=b$ <br />
$s \geq 0$ 

So, the reformulated problem becomes: <br />
**Objective**: Minimize $\frac{1}{2} x^T Q x + c^T x$ <br />
**Equality constraints**: $Ax + s = b$ <br />
**Non-negativity constraints**: $s \geq 0$

Now we're optimizing over both $x$ and $s$, with the inequality replaced by an equality plus a bound on $s$.

## Interior Point Method with Slack Variables

* **Barrier Formulation** <br />
   Since we still have inequality constraints (now $s \geq 0$), we apply a logarithmic barrier to enforce $s > 0$ (staying in the interior of the feasible region). The barrier-augmented objective becomes: <br />
   Minimize $\frac{1}{2} x^T Q x + c^T x - \mu \sum_{i=1}^m \log(s_i)$ <br />
   Subject to: $Ax + s = b$ <br />
   $\mu > 0$ is the barrier parameter.
   $-\log(s_i)$ grows large as $s_i \to 0^+$, keeping $s$ positive.
   This is now an equality-constrained problem with a barrier term to handle the non-negativity of $s$.

* **Lagrangian and KKT Conditions** <br />
   Form the Lagrangian to incorporate the equality constraint $Ax + s = b$:<br />
   $L(x, s, \lambda) = \frac{1}{2} x^T Q x + c^T x - \mu \sum_{i=1}^m \log(s_i) + \lambda^T (Ax + s - b)$ <br />
   Here, $\lambda$ is the vector of Lagrange multipliers for the equality constraints. The KKT conditions for this barrier problem are derived by taking partial derivatives w.r.t. $x$, $s$, and $\lambda$ respectively and setting them to zero:<br />
   $Qx + c + A^T \lambda = 0$ <br />
   $-\mu S^{-1} e + \lambda = 0$,<br /> 
   $Ax + s - b = 0$ <br />
   where $S = \text{diag}(s)$, $e = [1, 1, \dots, 1]^T$, and $S^{-1} e = [1/s_1, 1/s_2, \dots, 1/s_m]^T$ <br />
   From the second condition: $\lambda_i = \frac{\mu}{s_i} \quad \text{for} \quad i = 1, \dots, m$ This implies $\lambda > 0$ as long as $s > 0$ and $\mu > 0$, which aligns with the barrier's purpose.
   
   So, the system to solve is: <br />
   $Qx + c + A^T \lambda = 0$ <br />
   $S \lambda = \mu e$ (or component-wise: $s_i \lambda_i = \mu$) <br />
   $Ax + s = b$ <br />

* **Newton's Method**
   This is a nonlinear system in $x$, $s$, and $\lambda$. We solve it iteratively using Newton's method. Define the residuals: <br />
   $r_1 = Qx + c + A^T \lambda$<br />
   $r_2 = S \lambda - \mu e$<br />
   $r_3 = Ax + s - b$<br />
   We want $r_1 = 0$, $r_2 = 0$, $r_3 = 0$. Linearize the system around the current point $(x, s, \lambda)$ and compute the Newton step $(\Delta x, \Delta s, \Delta \lambda)$: <br />

   $$\begin{bmatrix}
   Q & 0 & A^T \\
   0 & \Lambda & S \\
   A & I & 0
   \end{bmatrix}
   \begin{bmatrix}
   \Delta x \\
   \Delta s \\
   \Delta \lambda
   \end{bmatrix}
   = -
   \begin{bmatrix}
   r_1 \\
   r_2 \\
   r_3
   \end{bmatrix}$$
   
   Where:
   $\Lambda = \text{diag}(\lambda)$, 
   $S = \text{diag}(s)$, 
   $I$ is the identity matrix.
   
   This system comes from the Jacobian of the KKT conditions. Solve it for the step direction, then update: <br />
   $x \leftarrow x + \alpha \Delta x$<br />
   $s \leftarrow s + \alpha \Delta s$<br />
   $\lambda \leftarrow \lambda + \alpha \Delta \lambda$
   
   Here, $\alpha$ is a step size (e.g., from a line search) to ensure $s$ remains positive and the residuals decrease.

* **Iterate and Reduce the Barrier**
   After each Newton step, check convergence for the current $\mu$.
   Reduce $\mu$ (e.g., $\mu \leftarrow 0.1 \mu$) to lessen the barrier's effect.
   Repeat until $\mu$ is small enough that $(x, s, \lambda)$ approximates the original problem's solution.

* **Recovering the Original Solution**
   As $\mu \to 0$:
   If $s_i \to 0$, then $\lambda_i$ may grow large, indicating an active constraint $(a_i^T x = b_i)$.
   If $\lambda_i \to 0$, then $s_i$ may remain positive, indicating an inactive constraint $(a_i^T x < b_i)$.
   The $x$ component converges to the optimal solution of the original QP.
