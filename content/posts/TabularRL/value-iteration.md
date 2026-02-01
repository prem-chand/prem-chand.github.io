---
title: "Value Iteration"
description: "Learn the Value Iteration algorithm for solving MDPs through iterative Bellman optimality backups"
date: 2026-02-01T10:01:00+00:00
draft: false
slug: "value-iteration"
categories:
  - Reinforcement Learning
  - Tutorial
tags:
  - RL
  - Dynamic Programming
  - Value Function
  - Bellman Equation
---

Value Iteration is a dynamic programming algorithm that computes the optimal value function by iteratively applying the Bellman optimality operator. Once converged, the optimal policy can be extracted directly from the value function.

---

## The Core Idea

The Bellman optimality equation tells us that the optimal value of a state equals the maximum expected return achievable from that state:

$$V^*(s) = \max_a \sum_{s'} P(s'|s,a) \left[ R(s,a,s') + \gamma V^*(s') \right]$$

Value Iteration turns this equation into an iterative update. Starting from arbitrary values, we repeatedly apply the Bellman backup until convergence:

$$V_{k+1}(s) \leftarrow \max_a \sum_{s'} P(s'|s,a) \left[ R(s,a,s') + \gamma V_k(s') \right]$$

The key insight: this operator is a **contraction mapping** with factor $\gamma$, guaranteeing convergence to $V^*$.

---

## Pseudocode

```
Algorithm: Value Iteration

Input:
    S          - finite set of states
    A          - finite set of actions
    P(s'|s,a)  - transition probability function
    R(s,a,s')  - reward function
    γ          - discount factor ∈ [0, 1)
    θ          - convergence threshold (small positive number)

Output:
    V*         - optimal value function
    π*         - optimal policy

Initialize:
    V(s) ← 0 for all s ∈ S  (or arbitrary values)

Repeat:
    Δ ← 0
    For each s ∈ S:
        v ← V(s)
        V(s) ← max_a [ Σ_{s'} P(s'|s,a) · (R(s,a,s') + γ·V(s')) ]
        Δ ← max(Δ, |v - V(s)|)
Until Δ < θ

# Extract optimal policy
For each s ∈ S:
    π*(s) ← argmax_a [ Σ_{s'} P(s'|s,a) · (R(s,a,s') + γ·V(s')) ]

Return V*, π*
```

---

## Python Implementation

```python
import numpy as np

def value_iteration(P, R, gamma, theta=1e-6):
    """
    Value Iteration algorithm.

    Args:
        P: Transition probabilities P[s, a, s'] = P(s'|s,a)
        R: Reward function R[s, a, s'] = R(s,a,s')
        gamma: Discount factor
        theta: Convergence threshold

    Returns:
        V: Optimal value function
        pi: Optimal policy
    """
    num_states, num_actions, _ = P.shape
    V = np.zeros(num_states)

    while True:
        delta = 0
        for s in range(num_states):
            v = V[s]
            # Bellman optimality backup
            q_values = np.zeros(num_actions)
            for a in range(num_actions):
                for s_next in range(num_states):
                    q_values[a] += P[s, a, s_next] * (R[s, a, s_next] + gamma * V[s_next])
            V[s] = np.max(q_values)
            delta = max(delta, abs(v - V[s]))

        if delta < theta:
            break

    # Extract optimal policy
    pi = np.zeros(num_states, dtype=int)
    for s in range(num_states):
        q_values = np.zeros(num_actions)
        for a in range(num_actions):
            for s_next in range(num_states):
                q_values[a] += P[s, a, s_next] * (R[s, a, s_next] + gamma * V[s_next])
        pi[s] = np.argmax(q_values)

    return V, pi
```

---

## Key Points

### 1. Bellman Optimality Backup

The core update applies the Bellman optimality operator $\mathcal{T}^*$, which contracts toward $V^*$ under the supremum norm with rate $\gamma$.

### 2. Convergence Guarantee

Since $\mathcal{T}^*$ is a $\gamma$-contraction, convergence is guaranteed. The error bound after $k$ iterations:

$$\|V_k - V^*\|_\infty \leq \gamma^k \|V_0 - V^*\|_\infty$$

### 3. Complexity

- **Per iteration:** $O(|S|^2|A|)$ for dense transitions
- **Number of iterations:** $O\left(\frac{1}{1-\gamma} \log \frac{1}{\theta}\right)$

### 4. In-Place vs Synchronous Updates

The pseudocode uses **synchronous updates** (all states updated before using new values). **In-place updates** (Gauss-Seidel style) often converge faster in practice and are equally valid since the contraction property still holds.

---

## When to Use Value Iteration

**Strengths:**
- Simple to implement
- Works well with large action spaces
- Guaranteed convergence

**Limitations:**
- Requires complete model (P and R)
- Must iterate over all states each sweep
- Computationally expensive for large state spaces

**Best for:** Problems where the model is known and the state space is manageable (thousands to millions of states with sparse transitions).

---

*Next: [Policy Iteration](../policy-iteration) — an alternative DP method that often converges in fewer iterations.*
