---
title: "SARSA: On-Policy TD Control"
description: "Learn the SARSA algorithm for on-policy temporal difference control"
date: 2026-02-01T10:04:00+00:00
draft: false
slug: "sarsa"
categories:
  - Reinforcement Learning
  - Tutorial
tags:
  - RL
  - Temporal Difference
  - SARSA
  - On-Policy
  - Model-Free
---

SARSA (State-Action-Reward-State-Action) is a temporal difference control algorithm that learns by bootstrapping from the next action actually taken by the policy. As an on-policy method, it learns the value of the policy being followed, including exploration.

---

## The Core Idea

SARSA combines the strengths of Monte Carlo (learning from experience) and Dynamic Programming (bootstrapping):

1. Take action $a$ in state $s$, observe reward $r$ and next state $s'$
2. Choose next action $a'$ using the same $\varepsilon$-greedy policy
3. Update Q using the **TD error**: the difference between the bootstrap target and current estimate

$$Q(s,a) \leftarrow Q(s,a) + \alpha \left[ r + \gamma Q(s',a') - Q(s,a) \right]$$

The name comes from the quintuple used in the update: $(S, A, R, S', A')$.

---

## Pseudocode

```
Algorithm: SARSA (State-Action-Reward-State-Action)

Input:
    γ          - discount factor ∈ [0, 1]
    α          - learning rate ∈ (0, 1]
    ε          - exploration rate ∈ (0, 1]
    num_episodes

Output:
    Q          - action-value function estimate
    π          - ε-greedy policy

Initialize:
    Q(s,a) ← arbitrary for all s ∈ S, a ∈ A
    Q(terminal, ·) ← 0

Repeat for each episode:
    s ← initial state
    a ← ε-greedy action from Q(s,·)

    While s is not terminal:
        s', r ← take action a, observe next state and reward
        a' ← ε-greedy action from Q(s',·)

        # TD update
        Q(s,a) ← Q(s,a) + α · [r + γ·Q(s',a') - Q(s,a)]

        s ← s'
        a ← a'

Until num_episodes reached

π(s) ← argmax_a Q(s,a)

Return Q, π
```

---

## Python Implementation

```python
import numpy as np
import random

def epsilon_greedy(Q, state, epsilon, num_actions):
    """Select action using epsilon-greedy policy."""
    if random.random() < epsilon:
        return random.randint(0, num_actions - 1)
    else:
        return np.argmax(Q[state])

def sarsa(env, num_episodes, gamma=0.99, alpha=0.1, epsilon=0.1):
    """
    SARSA: On-Policy TD Control.

    Args:
        env: Environment with reset() and step(action) methods
        num_episodes: Number of episodes to run
        gamma: Discount factor
        alpha: Learning rate
        epsilon: Exploration rate

    Returns:
        Q: Action-value function
        pi: Greedy policy
    """
    num_states = env.observation_space.n
    num_actions = env.action_space.n

    Q = np.zeros((num_states, num_actions))

    for episode in range(num_episodes):
        state = env.reset()
        action = epsilon_greedy(Q, state, epsilon, num_actions)

        done = False
        while not done:
            next_state, reward, done, _ = env.step(action)

            if done:
                # Terminal state: no future value
                td_target = reward
            else:
                next_action = epsilon_greedy(Q, next_state, epsilon, num_actions)
                td_target = reward + gamma * Q[next_state, next_action]

            # TD update
            td_error = td_target - Q[state, action]
            Q[state, action] += alpha * td_error

            state = next_state
            if not done:
                action = next_action

    # Extract greedy policy
    pi = np.argmax(Q, axis=1)

    return Q, pi
```

---

## Key Points

### 1. The TD Error

$$\delta_t = r_{t+1} + \gamma Q(s_{t+1}, a_{t+1}) - Q(s_t, a_t)$$

This is the one-step bootstrap estimate minus the current estimate. Positive TD errors mean we underestimated; negative means we overestimated.

### 2. On-Policy Learning

SARSA uses the same policy for both:
- **Behavior:** Selecting actions during episodes
- **Target:** Computing the bootstrap value

This means SARSA learns $Q^{\pi_\varepsilon}$, not $Q^*$. The learned Q-values reflect the cost of exploration.

### 3. Bootstrapping Trade-off

Unlike Monte Carlo, SARSA updates at every step using estimated values:
- **Lower variance:** One step of randomness vs. entire episode
- **Introduces bias:** We're using estimates, not true returns

### 4. Learning Rate

- **Constant $\alpha$:** Tracks non-stationarity but doesn't fully converge
- **Decaying $\alpha$:** Robbins-Monro conditions ($\sum \alpha = \infty$, $\sum \alpha^2 < \infty$) guarantee convergence

### 5. Terminal State Handling

When $s'$ is terminal, the target is just $r$ (no future value to bootstrap from).

---

## SARSA vs Monte Carlo

| Aspect | SARSA | Monte Carlo |
|--------|-------|-------------|
| Update timing | Every step | End of episode |
| Bias | Yes (bootstrapping) | No |
| Variance | Lower | Higher |
| Works for | Continuing + episodic | Episodic only |
| Sample efficiency | Higher | Lower |

---

## The Cliff Walking Example

SARSA's on-policy nature makes it **safer** in dangerous environments. Consider a cliff-walking problem:

```
Start → [ ][ ][ ][ ][ ][ ][ ][ ][ ][ ][ ] → Goal
        [C][C][C][C][C][C][C][C][C][C][C]  (Cliff: -100 reward)
```

- **SARSA:** Learns to take the safe path along the top, accounting for occasional random exploratory moves that might fall off the cliff
- **Q-Learning:** Learns the optimal path along the cliff edge, which is risky during exploration

SARSA's learned policy reflects the reality of $\varepsilon$-greedy exploration.

---

## When to Use SARSA

**Strengths:**
- Safer in risky environments
- Accounts for exploration cost
- Online learning (updates every step)

**Limitations:**
- Doesn't learn optimal policy directly
- Performance depends on exploration strategy

**Best for:** Problems where exploration has real costs (robotics, trading), or when you want the learned policy to be safe even with occasional random actions.

---

*Next: [Q-Learning](../q-learning) — learn the optimal policy directly with off-policy TD control.*
