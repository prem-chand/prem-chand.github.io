---
title: "On-Policy First-Visit Monte Carlo Control"
description: "Learn Monte Carlo methods for reinforcement learning using complete episode returns"
date: 2026-02-01T10:03:00+00:00
draft: false
slug: "on-policy-monte-carlo-control"
categories:
  - Reinforcement Learning
  - Tutorial
tags:
  - RL
  - Monte Carlo
  - Model-Free
  - On-Policy
---

Monte Carlo methods learn from complete episodes of experience without requiring a model of the environment. On-Policy First-Visit MC Control uses an $\varepsilon$-greedy policy to explore while updating Q-values based on the returns observed from first visits to state-action pairs.

---

## The Core Idea

Instead of computing expected values from a known model, Monte Carlo methods **estimate** values by averaging observed returns:

$$Q(s,a) \approx \mathbb{E}[G_t | S_t = s, A_t = a]$$

The algorithm:
1. Generate an episode using the current policy
2. For each state-action pair visited (first time only), compute the return $G$
3. Update Q-values by averaging all observed returns
4. Improve the policy to be $\varepsilon$-greedy with respect to Q

This requires complete episodes—we must reach a terminal state to compute returns.

---

## Pseudocode

```
Algorithm: On-Policy First-Visit MC Control (ε-greedy)

Input:
    γ          - discount factor ∈ [0, 1]
    ε          - exploration rate ∈ (0, 1]
    num_episodes

Output:
    Q          - action-value function estimate
    π          - ε-greedy policy (approximately optimal)

Initialize:
    Q(s,a) ← arbitrary for all s ∈ S, a ∈ A
    Returns(s,a) ← empty list for all s ∈ S, a ∈ A

Repeat for each episode:
    #---------------------------
    # Generate episode using π (ε-greedy w.r.t. Q)
    #---------------------------
    episode ← []
    s ← initial state
    While s is not terminal:
        a ← ε-greedy action from Q(s,·)
             // with prob ε: random action
             // with prob 1-ε: argmax_a Q(s,a)
        s', r ← take action a, observe next state and reward
        episode.append((s, a, r))
        s ← s'

    #---------------------------
    # Compute returns and update Q
    #---------------------------
    G ← 0
    visited ← empty set

    For t = len(episode)-1 down to 0:
        s_t, a_t, r_{t+1} ← episode[t]
        G ← γ·G + r_{t+1}

        If (s_t, a_t) not in visited:      # first-visit check
            visited.add((s_t, a_t))
            Returns(s_t, a_t).append(G)
            Q(s_t, a_t) ← mean(Returns(s_t, a_t))

Until convergence or num_episodes reached

# Final policy (can be made greedy, ε=0)
π(s) ← argmax_a Q(s,a)

Return Q, π
```

---

## Python Implementation

```python
import numpy as np
from collections import defaultdict
import random

def epsilon_greedy(Q, state, epsilon, num_actions):
    """Select action using epsilon-greedy policy."""
    if random.random() < epsilon:
        return random.randint(0, num_actions - 1)
    else:
        return np.argmax(Q[state])

def mc_control_first_visit(env, num_episodes, gamma=0.99, epsilon=0.1):
    """
    On-Policy First-Visit Monte Carlo Control.

    Args:
        env: Environment with reset() and step(action) methods
        num_episodes: Number of episodes to run
        gamma: Discount factor
        epsilon: Exploration rate

    Returns:
        Q: Action-value function as dict
        pi: Greedy policy as dict
    """
    num_actions = env.action_space.n

    # Initialize Q-values and returns
    Q = defaultdict(lambda: np.zeros(num_actions))
    returns = defaultdict(list)

    for episode in range(num_episodes):
        # Generate episode
        trajectory = []
        state = env.reset()
        done = False

        while not done:
            action = epsilon_greedy(Q, state, epsilon, num_actions)
            next_state, reward, done, _ = env.step(action)
            trajectory.append((state, action, reward))
            state = next_state

        # Compute returns and update Q (backward pass)
        G = 0
        visited = set()

        for t in range(len(trajectory) - 1, -1, -1):
            state, action, reward = trajectory[t]
            G = gamma * G + reward

            # First-visit check
            if (state, action) not in visited:
                visited.add((state, action))
                returns[(state, action)].append(G)
                Q[state][action] = np.mean(returns[(state, action)])

    # Extract greedy policy
    pi = {s: np.argmax(Q[s]) for s in Q}

    return dict(Q), pi
```

---

## Key Points

### 1. First-Visit vs Every-Visit

- **First-visit:** Updates Q only on the first occurrence of $(s,a)$ in an episode
- **Every-visit:** Updates on all occurrences

Both converge to the true value. First-visit has cleaner theoretical properties (unbiased estimates), while every-visit often has lower variance in practice.

### 2. Why $\varepsilon$-greedy?

On-policy MC requires continued exploration to ensure all state-action pairs are visited. Without $\varepsilon > 0$, you can get stuck with a suboptimal deterministic policy that never visits some states.

### 3. Incremental Mean Update

For memory efficiency, replace the returns list with an incremental update:

```python
N[s, a] += 1
Q[s, a] += (1 / N[s, a]) * (G - Q[s, a])
```

This avoids storing all returns and computes the running mean online.

### 4. GLIE Convergence

**Greedy in the Limit with Infinite Exploration (GLIE):** If $\varepsilon$ decays to 0 such that all state-action pairs are visited infinitely often, MC control converges to $Q^*$.

A common schedule: $\varepsilon_k = \frac{1}{k}$ where $k$ is the episode number.

### 5. No Bootstrapping

Unlike TD methods, MC waits for the full episode return. This means:
- **High variance** (returns depend on many random actions)
- **Zero bias** (uses true returns, not estimates)

---

## Monte Carlo vs Dynamic Programming

| Aspect | Dynamic Programming | Monte Carlo |
|--------|---------------------|-------------|
| Model required | Yes (P, R) | No |
| Update timing | Synchronous sweeps | After each episode |
| Bias | None | None |
| Variance | None (exact) | High |
| Works for | Known MDPs | Any episodic task |

---

## When to Use Monte Carlo

**Strengths:**
- No model required
- Unbiased value estimates
- Simple to implement
- Works well when episodes are short

**Limitations:**
- Only works for episodic tasks
- High variance requires many episodes
- Must wait until episode end to learn

**Best for:** Episodic tasks where you can afford to wait for complete trajectories, or when bootstrapping introduces too much bias (e.g., partially observable environments).

---

*Next: [SARSA](../sarsa) — combine the best of MC and DP with temporal difference learning.*
