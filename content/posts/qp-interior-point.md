---
title: "Primal-Dual Predictor-Corrector Interior Point Method for Batched Quadratic Programming"
description: "A comprehensive walkthrough of the Mehrotra Predictor-Corrector IPM for solving batched QP problems, covering KKT conditions, Newton steps, and Schur complement factorization."
date: 2025-03-23T10:00:00+00:00
draft: false
slug: "qp-interior-point"
categories:
  - Optimization
  - Control Systems
tags:
  - Quadratic Programming
  - Interior Point Method
  - KKT Conditions
  - Optimization
  - Numerical Methods
  - Control Theory
---

This document provides a comprehensive explanation of the method, which solves a batch of Quadratic Programming (QP) problems using a Primal-Dual Predictor-Corrector Interior Point Method (IPM).

Consider the inequality-constrained quadratic program (QP) and its solution via the interior point method by introducing slack variables. Slack variables are a handy way to convert inequality constraints into equality constraints, which can simplify the problem's structure and make it more intuitive to handle in some contexts.

## Problem Statement

We start with the QP: <br />
**Objective**: Minimize $f(x)= \frac{1}{2} x^T Q x + c^T x$ <br />
**Constraints**: $Ax \leq b$ <br />
Where:
* $x \in \mathbb{R}^n$: the decision variable, <br />
* $Q \in \mathbb{R}^{n \times n}$: Positive semi-definite quadratic cost matrix, <br />
* $c \in \mathbb{R}^n$: Linear cost vector, <br />
* $A \in \mathbb{R}^{m \times n}$: Constraint matrix, <br />
* $b \in \mathbb{R}^m$: Constraint right-hand side.

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

## Algorithm: Primal-Dual Predictor-Corrector IPM
The algorithm uses a Primal-Dual Predictor-Corrector Interior Point Method, an advanced variant of the Mehrotra Predictor-Corrector algorithm. It iteratively solves the KKT optimality conditions, balancing feasibility and optimality via predictor and corrector steps.

**KKT Conditions** <br />
For the QP problem, the KKT conditions are:
* Primal Feasibility: $Ax + s = b$,
* Dual Feasibility: $Qx + c + A^T \lambda = 0$,
* Complementary Slackness: $s_i \lambda_i = 0 ~\forall i$   (relaxed to $s_i \lambda_i = \mu$
 in IPM, where $\mu$
 is the barrier parameter).

The algorithm approximates these conditions by introducing a barrier parameter $\mu$, which is driven to zero as iterations progress.

**Newton's Method**
   This is a nonlinear system in $x$, $s$, and $\lambda$. We solve it iteratively using Newton's method. Define the residuals: <br />
   * Primal Residual: $r_b = Ax + s - b $<br />
   * Dual Residual: $r_d = Qx + c + A^T \lambda $<br />
   * Complementary Slackness Residual: $r_{sy} = S \lambda$<br />
   * Duality gap: $\rm{gap} = \Sigma_i s_i \lambda_i$
   * Barrier parameter: $\mu = \frac{\rm{gap}}{m}$

   We want $r_d = 0$, $r_b = 0$, $r_{sy} = 0$. Linearize the system around the current point $(x, \lambda)$ and compute the Newton step $(\Delta x, \Delta \lambda)$: <br />

   $$\begin{bmatrix}
   Q & A^\top \\
   A & -S \Lambda^{-1}
   \end{bmatrix}
   \begin{bmatrix}
   \Delta x \\
   \Delta \lambda
   \end{bmatrix}=
   \begin{bmatrix}
   -r_d \\
   -r_b + r_{sy} \Lambda^{-1}
   \end{bmatrix},$$

   Where:
   $\Lambda = \text{diag}(\lambda)$,
   $S = \text{diag}(s)$.

   Using the Schur complement:
   * Cholesky factorization: $Q = LL^T$<br />
   * Intermediate solve: $X = Q^{-1} A^T$ (using cholesky)<br />
   * Right-hand Side: $z = -Q^{-1} r_d$<br />
   * Schur complement: $M = A Q^{-1} A^T + D_{\rho}$<br />

  **Predictor Step**<br />
  Solve the system for $(\Delta x, \Delta \lambda)$ using the Cholesky factorization of $M$.
