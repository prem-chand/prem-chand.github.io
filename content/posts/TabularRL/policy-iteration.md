---
title: "Policy Iteration"
description: "Learn the Policy Iteration algorithm with alternating policy evaluation and improvement steps"
date: 2026-02-01T10:02:00+00:00
draft: false
slug: "policy-iteration"
categories:
  - Reinforcement Learning
  - Tutorial
tags:
  - RL
  - Dynamic Programming
  - Policy Evaluation
  - Policy Improvement
---

Policy Iteration is a dynamic programming algorithm that alternates between two phases: **policy evaluation** (computing the value function for the current policy) and **policy improvement** (making the policy greedy with respect to the value function). This process converges to the optimal policy in a finite number of steps.

---

## The Core Idea

Instead of finding $V^*$ directly, Policy Iteration works with policies:

1. **Evaluate:** Given policy $\pi$, compute $V^\pi$ (what values do we get following $\pi$?)
2. **Improve:** Create a better policy $\pi'$ by acting greedily with respect to $V^\pi$
3. **Repeat:** Until the policy stops changing

The **Policy Improvement Theorem** guarantees that each greedy improvement produces a policy at least as good as the previous one. Since there are finitely many deterministic policies, we must eventually reach the optimum.

---

## Pseudocode

```
Algorithm: Policy Iteration

Input:
    S          - finite set of states
    A          - finite set of actions
    P(s'|s,a)  - transition probability function
    R(s,a,s')  - reward function
    γ          - discount factor ∈ [0, 1)
    θ          - threshold for policy evaluation convergence

Output:
    V*         - optimal value function
    π*         - optimal policy

Initialize:
    π(s) ← arbitrary action for all s ∈ S

Repeat:
    #---------------------------
    # Policy Evaluation
    #---------------------------
    Repeat:
        Δ ← 0
        For each s ∈ S:
            v ← V(s)
            V(s) ← Σ_{s'} P(s'|s,π(s)) · (R(s,π(s),s') + γ·V(s'))
            Δ ← max(Δ, |v - V(s)|)
    Until Δ < θ

    #---------------------------
    # Policy Improvement
    #---------------------------
    policy_stable ← True
    For each s ∈ S:
        old_action ← π(s)
        π(s) ← argmax_a [ Σ_{s'} P(s'|s,a) · (R(s,a,s') + γ·V(s')) ]
        If old_action ≠ π(s):
            policy_stable ← False

Until policy_stable

Return V, π
```

---

## Python Implementation

```python
import numpy as np

def policy_iteration(P, R, gamma, theta=1e-6):
    """
    Policy Iteration algorithm.

    Args:
        P: Transition probabilities P[s, a, s'] = P(s'|s,a)
        R: Reward function R[s, a, s'] = R(s,a,s')
        gamma: Discount factor
        theta: Convergence threshold for policy evaluation

    Returns:
        V: Optimal value function
        pi: Optimal policy
    """
    num_states, num_actions, _ = P.shape

    # Initialize arbitrary policy
    pi = np.zeros(num_states, dtype=int)
    V = np.zeros(num_states)

    while True:
        # Policy Evaluation
        while True:
            delta = 0
            for s in range(num_states):
                v = V[s]
                a = pi[s]
                V[s] = sum(
                    P[s, a, s_next] * (R[s, a, s_next] + gamma * V[s_next])
                    for s_next in range(num_states)
                )
                delta = max(delta, abs(v - V[s]))
            if delta < theta:
                break

        # Policy Improvement
        policy_stable = True
        for s in range(num_states):
            old_action = pi[s]

            # Compute Q-values for all actions
            q_values = np.zeros(num_actions)
            for a in range(num_actions):
                for s_next in range(num_states):
                    q_values[a] += P[s, a, s_next] * (R[s, a, s_next] + gamma * V[s_next])

            pi[s] = np.argmax(q_values)

            if old_action != pi[s]:
                policy_stable = False

        if policy_stable:
            break

    return V, pi
```

---

## Key Points

### 1. Policy Evaluation

Solves the linear system $V^\pi = \mathcal{T}^\pi V^\pi$ iteratively. Alternatively, solve directly via matrix inversion:

$$V^\pi = (I - \gamma P^\pi)^{-1} R^\pi$$

This is $O(|S|^3)$ but exact in one step.

### 2. Policy Improvement Theorem

If $Q^\pi(s, \pi'(s)) \geq V^\pi(s)$ for all $s$, then $V^{\pi'} \geq V^\pi$. Greedy improvement satisfies this with equality only at optimality.

### 3. Convergence

Guaranteed in at most $|A|^{|S|}$ iterations (finite policy space), but typically converges in very few iterations—often surprisingly fast.

### 4. Stochastic Policy Evaluation

For stochastic policies $\pi(a|s)$, the evaluation step becomes:

```
V(s) ← Σ_a π(a|s) · Σ_{s'} P(s'|s,a) · (R(s,a,s') + γ·V(s'))
```

---

## Value Iteration vs Policy Iteration

| Aspect | Value Iteration | Policy Iteration |
|--------|-----------------|------------------|
| Per iteration cost | $O(\|S\|^2\|A\|)$ | $O(\|S\|^2\|A\| + \|S\|^3)$ or iterative eval |
| Number of iterations | Many (depends on $\gamma$, $\theta$) | Few (policy space is finite) |
| Best when | Large action space, simple transitions | Small state space, need exact $V^\pi$ |

---

## Generalized Policy Iteration

Value Iteration and Policy Iteration are two extremes of a spectrum:

- **Policy Iteration:** Full evaluation before each improvement
- **Value Iteration:** Single backup (zero-step evaluation) before improvement

Any interleaving of partial evaluation and improvement converges—this is called **Generalized Policy Iteration (GPI)**. Most practical RL algorithms (including TD methods) can be viewed through the GPI lens.

---

## When to Use Policy Iteration

**Strengths:**
- Often converges in fewer iterations than Value Iteration
- Can use matrix methods for exact policy evaluation
- Clear separation of evaluation and improvement

**Limitations:**
- Each iteration is more expensive (full policy evaluation)
- Requires complete model (P and R)

**Best for:** Problems with small state spaces where you want exact policy evaluation, or when the number of iterations is the bottleneck.

---

*Next: [Monte Carlo Control](../on-policy-monte-carlo-control) — move to model-free learning from experience.*
