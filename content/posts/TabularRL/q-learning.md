---
title: "Q-Learning: Off-Policy TD Control"
description: "Learn the Q-Learning algorithm for off-policy temporal difference control"
date: 2026-02-01T10:05:00+00:00
draft: false
slug: "q-learning"
categories:
  - Reinforcement Learning
  - Tutorial
tags:
  - RL
  - Temporal Difference
  - Q-Learning
  - Off-Policy
  - Model-Free
---

Q-Learning is the most famous reinforcement learning algorithm. It learns the optimal action-value function directly, regardless of the policy being followed. This off-policy property makes Q-Learning remarkably versatile and forms the foundation for many modern deep RL algorithms.

---

## The Core Idea

Q-Learning updates toward the **best possible** next action, not the action actually taken:

$$Q(s,a) \leftarrow Q(s,a) + \alpha \left[ r + \gamma \max_{a'} Q(s',a') - Q(s,a) \right]$$

The key difference from SARSA: we use $\max_{a'} Q(s',a')$ instead of $Q(s',a')$ where $a'$ is the action we would take.

This means:
- **Behavior policy:** Can be anything (usually $\varepsilon$-greedy for exploration)
- **Target policy:** Always greedy (optimal)

Q-Learning directly learns $Q^*$ even while exploring.

---

## Pseudocode

```
Algorithm: Q-Learning (Off-Policy TD Control)

Input:
    γ          - discount factor ∈ [0, 1]
    α          - learning rate ∈ (0, 1]
    ε          - exploration rate ∈ (0, 1]
    num_episodes

Output:
    Q          - action-value function estimate (converges to Q*)
    π          - optimal policy

Initialize:
    Q(s,a) ← arbitrary for all s ∈ S, a ∈ A
    Q(terminal, ·) ← 0

Repeat for each episode:
    s ← initial state

    While s is not terminal:
        a ← ε-greedy action from Q(s,·)      # behavior policy
        s', r ← take action a, observe next state and reward

        # TD update (greedy target)
        Q(s,a) ← Q(s,a) + α · [r + γ · max_{a'} Q(s',a') - Q(s,a)]

        s ← s'

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

def q_learning(env, num_episodes, gamma=0.99, alpha=0.1, epsilon=0.1):
    """
    Q-Learning: Off-Policy TD Control.

    Args:
        env: Environment with reset() and step(action) methods
        num_episodes: Number of episodes to run
        gamma: Discount factor
        alpha: Learning rate
        epsilon: Exploration rate

    Returns:
        Q: Optimal action-value function
        pi: Optimal policy
    """
    num_states = env.observation_space.n
    num_actions = env.action_space.n

    Q = np.zeros((num_states, num_actions))

    for episode in range(num_episodes):
        state = env.reset()
        done = False

        while not done:
            action = epsilon_greedy(Q, state, epsilon, num_actions)
            next_state, reward, done, _ = env.step(action)

            if done:
                td_target = reward
            else:
                td_target = reward + gamma * np.max(Q[next_state])

            # TD update
            Q[state, action] += alpha * (td_target - Q[state, action])
            state = next_state

    # Extract optimal policy
    pi = np.argmax(Q, axis=1)

    return Q, pi
```

---

## Key Points

### 1. Off-Policy Learning

Q-Learning separates:
- **Behavior policy:** What we do (e.g., $\varepsilon$-greedy)
- **Target policy:** What we learn (greedy/optimal)

This enables learning from exploratory behavior, replayed experiences, or even demonstrations.

### 2. Convergence

Q-Learning is guaranteed to converge to $Q^*$ under Robbins-Monro conditions on $\alpha$, provided all state-action pairs are visited infinitely often.

### 3. Simpler Loop

Unlike SARSA, Q-Learning doesn't need to track the next action for the update—we just take the max:

```python
# SARSA: need a' before update
a' = epsilon_greedy(Q[s'], epsilon)
Q[s,a] += alpha * (r + gamma * Q[s',a'] - Q[s,a])

# Q-Learning: no a' needed
Q[s,a] += alpha * (r + gamma * max(Q[s']) - Q[s,a])
```

### 4. Maximization Bias

Q-Learning can **overestimate** values due to using max over noisy estimates:

$$\mathbb{E}[\max_a Q(s',a)] \geq \max_a \mathbb{E}[Q(s',a)]$$

The max operator preferentially selects overestimated actions. This is addressed by [Double Q-Learning](../double-q-learning).

---

## Q-Learning vs SARSA

| Aspect | SARSA | Q-Learning |
|--------|-------|------------|
| Target | $r + \gamma Q(s', a')$ where $a' \sim \pi$ | $r + \gamma \max_{a'} Q(s', a')$ |
| Policy type | On-policy | Off-policy |
| Converges to | $Q^{\pi_\varepsilon}$ | $Q^*$ (regardless of behavior policy) |
| Update timing | After selecting $a'$ | Before selecting next action |
| Safety | Safer during learning | More aggressive |

---

## The Cliff Walking Example (Revisited)

In the cliff environment:

```
Start → [ ][ ][ ][ ][ ][ ][ ][ ][ ][ ][ ] → Goal
        [C][C][C][C][C][C][C][C][C][C][C]  (Cliff: -100 reward)
```

- **Q-Learning:** Learns the optimal path along the cliff edge (shortest)
- **SARSA:** Learns a safer path away from the cliff

During training, Q-Learning might suffer more cliff falls (due to $\varepsilon$-greedy exploration), but its final greedy policy is optimal. The learned Q-values reflect what's optimal assuming perfect execution.

---

## Why Q-Learning Matters

Q-Learning is the foundation for:

1. **Deep Q-Networks (DQN):** Replace the Q-table with a neural network
2. **Double DQN:** Address maximization bias in deep RL
3. **Prioritized Experience Replay:** Learn more from important transitions
4. **Rainbow:** Combine multiple improvements

Understanding tabular Q-Learning deeply is essential before scaling to function approximation.

---

## When to Use Q-Learning

**Strengths:**
- Learns optimal policy directly
- Can learn from any exploration strategy
- Simple and well-understood

**Limitations:**
- Maximization bias can slow convergence
- Aggressive in risky environments during learning

**Best for:** General-purpose control where you want the optimal policy and can tolerate some exploration cost during training.

---

*Next: [Double Q-Learning](../double-q-learning) — eliminate maximization bias for more stable learning.*
